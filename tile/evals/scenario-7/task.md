# OAuth 2.0 PKCE Authorization Flow

A Fastify application that implements the OAuth 2.0 Authorization Code flow with PKCE (Proof Key for Code Exchange), suitable for public clients that cannot store a client secret securely.

## Capabilities

### Generate PKCE parameters

- Generates a cryptographically random `code_verifier` of at least 43 characters [@test](./tests/pkce-verifier.test.ts)
- Derives a `code_challenge` from the verifier using the S256 method (SHA-256, then base64url-encode) [@test](./tests/pkce-challenge.test.ts)
- The `code_challenge_method` parameter sent to the authorization server is `S256` [@test](./tests/pkce-method.test.ts)

### Build authorization redirect

- `GET /auth/login` redirects to the authorization server with `response_type=code`, `client_id`, `redirect_uri`, `scope`, `state`, and the PKCE challenge parameters [@test](./tests/login-redirect.test.ts)
- The `state` parameter is a random value stored in the session to prevent CSRF [@test](./tests/state-csrf.test.ts)

### Exchange authorization code for tokens

- `GET /auth/callback` validates that the returned `state` matches the stored value; mismatched state returns HTTP 400 [@test](./tests/callback-state-mismatch.test.ts)
- On valid state, sends the authorization code and `code_verifier` to the token endpoint and stores the received tokens [@test](./tests/callback-token-exchange.test.ts)

## Implementation

[@generates](./src/auth.ts)

## API

```typescript { #api }
import { FastifyInstance } from 'fastify'

export async function oauthPlugin(app: FastifyInstance): Promise<void>
```

## Dependencies { .dependencies }

### fastify 1.0.0 { .dependency }

Fast and low overhead web framework for Node.js. Used for route registration, reply.redirect(), and request session handling.

[@satisfied-by](fastify)

### @matteo.collina/skills 1.0.0 { .dependency }

Provides OAuth 2.0 skill definitions including PKCE flow guidance, security checklists, and RFC references.

[@satisfied-by](@matteo.collina/skills)
