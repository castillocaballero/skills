# OAuth Skill

The `oauth` skill implements OAuth 2.0/2.1 authorization flows in Fastify applications — configures authorization code with PKCE, client credentials, device flow, refresh token rotation, JWT validation, and token introspection/revocation endpoints.

## Skill Reference

```text { .api }
Skill name:  oauth
Tile name:   mcollina/oauth
Version:     0.1.0
Path:        skills/oauth/SKILL.md
Tile:        skills/oauth/tile.json
Tags:        oauth, oauth2, security, authentication, authorization, jwt, fastify
```

## When to Use

- Implementing or debugging an OAuth 2.0/2.1 flow in a Fastify application
- Validating tokens, configuring PKCE, or setting up refresh token rotation
- Securing Fastify routes and plugins with access-control middleware
- Resolving RFC compliance questions or identifying security anti-patterns

**Trigger terms**: authentication, authorization, login flows, access tokens, API security, securing Fastify routes, token validation errors, mismatched redirect URIs, CSRF, scope problems, RFC 6749/6750/7636/8252/8628

## Capabilities

### Authorization Code + PKCE Flow

```typescript { .api }
// Install:
// npm install @fastify/oauth2 @fastify/cookie @fastify/session fastify-plugin

// plugins/oauth.ts
import fp from 'fastify-plugin'
import oauth2 from '@fastify/oauth2'
import { FastifyInstance } from 'fastify'

export default fp(async function (fastify: FastifyInstance) {
  fastify.register(oauth2, {
    name: 'oauth2',
    scope: ['openid', 'profile', 'email'],
    credentials: {
      client: {
        id: process.env.CLIENT_ID!,
        secret: process.env.CLIENT_SECRET!,
      },
      auth: {
        authorizeHost: process.env.AUTH_SERVER!,
        authorizePath: '/authorize',
        tokenHost: process.env.AUTH_SERVER!,
        tokenPath: '/token',
      },
    },
    startRedirectPath: '/login',
    callbackUri: `${process.env.APP_URL}/auth/callback`,
    pkce: 'S256',          // Enable PKCE with SHA-256 challenge
    generateStateFunction: (request) => request.session.get('state'),
    checkStateFunction: (request, callback) => {
      if (request.query.state === request.session.get('state')) {
        callback()
      } else {
        callback(new Error('Invalid state'))
      }
    },
  })
})
```

### Callback Route and Token Exchange

```typescript { .api }
// routes/auth.ts
fastify.get('/auth/callback', async (request, reply) => {
  const { token } = await fastify.oauth2.getAccessTokenFromAuthorizationCodeFlow(request)
  // token.access_token: string
  // token.token_type: string
  // token.expires_in: number
  // token.refresh_token?: string
  // token.id_token?: string (OpenID Connect)
  request.session.set('token', token)
  reply.redirect('/')
})
```

### Client Credentials Flow

```typescript { .api }
// For machine-to-machine (M2M) API access
const response = await fetch(`${AUTH_SERVER}/token`, {
  method: 'POST',
  headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
  body: new URLSearchParams({
    grant_type: 'client_credentials',
    client_id: process.env.CLIENT_ID!,
    client_secret: process.env.CLIENT_SECRET!,
    scope: 'api:read api:write',
  }),
})
const { access_token, expires_in } = await response.json()
```

### JWT Validation

```typescript { .api }
// Install: npm install @fastify/jwt
import jwt from '@fastify/jwt'

fastify.register(jwt, {
  secret: { public: process.env.JWT_PUBLIC_KEY! },   // RS256/ES256
  // OR for HMAC:
  // secret: process.env.JWT_SECRET!,
  verify: {
    algorithms: ['RS256'],
    issuer: process.env.AUTH_SERVER,
    audience: process.env.CLIENT_ID,
  }
})

// Protect routes
fastify.addHook('onRequest', async (request, reply) => {
  await request.jwtVerify()   // throws 401 if invalid
})

// Access payload
// request.user: JwtPayload (decoded token)
```

### Refresh Token Rotation

```typescript { .api }
async function refreshAccessToken(refreshToken: string) {
  const response = await fetch(`${AUTH_SERVER}/token`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
    body: new URLSearchParams({
      grant_type: 'refresh_token',
      refresh_token: refreshToken,
      client_id: process.env.CLIENT_ID!,
      client_secret: process.env.CLIENT_SECRET!,
    }),
  })
  if (!response.ok) throw new Error('Token refresh failed')
  return response.json()
  // Returns: { access_token, refresh_token (new), expires_in, token_type }
}
```

### Device Flow (RFC 8628)

```typescript { .api }
// Step 1: Get device code
const deviceResponse = await fetch(`${AUTH_SERVER}/device/code`, {
  method: 'POST',
  headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
  body: new URLSearchParams({
    client_id: process.env.CLIENT_ID!,
    scope: 'openid profile',
  }),
})
const { device_code, user_code, verification_uri, interval } = await deviceResponse.json()

// Step 2: Poll for token
// Poll every `interval` seconds until authorized or expired
const tokenResponse = await fetch(`${AUTH_SERVER}/token`, {
  method: 'POST',
  body: new URLSearchParams({
    grant_type: 'urn:ietf:params:oauth:grant-type:device_code',
    device_code,
    client_id: process.env.CLIENT_ID!,
  }),
})
```

### Token Introspection (RFC 7662)

```typescript { .api }
async function introspectToken(token: string) {
  const response = await fetch(`${AUTH_SERVER}/introspect`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/x-www-form-urlencoded',
      'Authorization': `Basic ${Buffer.from(`${CLIENT_ID}:${CLIENT_SECRET}`).toString('base64')}`,
    },
    body: new URLSearchParams({ token }),
  })
  return response.json()
  // Returns: { active: boolean, scope?, client_id?, username?, exp?, sub?, ... }
}
```

### Token Revocation (RFC 7009)

```typescript { .api }
async function revokeToken(token: string, tokenTypeHint?: 'access_token' | 'refresh_token') {
  await fetch(`${AUTH_SERVER}/revoke`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/x-www-form-urlencoded',
      'Authorization': `Basic ${Buffer.from(`${CLIENT_ID}:${CLIENT_SECRET}`).toString('base64')}`,
    },
    body: new URLSearchParams({
      token,
      ...(tokenTypeHint && { token_type_hint: tokenTypeHint }),
    }),
  })
}
```

## OAuth Security Checklist

```text { .api }
Required for production:
- Always use PKCE (S256) for public clients (RFC 7636)
- Validate state parameter on every callback to prevent CSRF
- Validate iss, aud, exp, nbf claims in JWT tokens
- Use HTTPS for all redirect URIs
- Store tokens in HttpOnly, Secure, SameSite=Lax cookies (not localStorage)
- Implement refresh token rotation (new refresh token on each use)
- Revoke tokens on logout

Anti-patterns to avoid:
- Implicit flow (deprecated in OAuth 2.1)
- Resource Owner Password Credentials (deprecated in OAuth 2.1)
- Storing tokens in localStorage or sessionStorage
- Skipping state validation
- Using plain PKCE challenge (use S256)
```

## Tile Manifest

```json { .api }
// skills/oauth/tile.json
{
  "name": "mcollina/oauth",
  "version": "0.1.0",
  "private": false,
  "summary": "Implements OAuth 2.0/2.1 authorization flows in Fastify applications.",
  "skills": {
    "oauth": {
      "path": "SKILL.md"
    }
  }
}
```
