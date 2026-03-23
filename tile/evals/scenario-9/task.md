# JWT Authentication Middleware for Fastify

A Fastify plugin that verifies JWT tokens on protected routes, attaches the decoded user to the request object, and returns appropriate HTTP error responses for missing or invalid tokens.

## Capabilities

### Verify JWT on incoming requests

- A request to a protected route with a valid `Authorization: Bearer <token>` header proceeds to the handler [@test](./tests/valid-token.test.ts)
- A request to a protected route without an Authorization header returns HTTP 401 [@test](./tests/missing-token.test.ts)
- A request with a malformed or expired JWT returns HTTP 401 [@test](./tests/invalid-token.test.ts)

### Attach user context to the request

- After successful verification, the decoded JWT payload is available on the request object inside the route handler [@test](./tests/user-context.test.ts)

### Protect routes selectively

- Public routes (e.g. `GET /health`) are accessible without a token [@test](./tests/public-route.test.ts)
- Protected routes (e.g. `GET /me`) require a valid token [@test](./tests/protected-route.test.ts)

### Role-based access control

- A route that requires the `admin` role returns HTTP 403 when a valid token with a non-admin role is provided [@test](./tests/rbac-forbidden.test.ts)
- A route that requires the `admin` role succeeds when the token payload contains `role: 'admin'` [@test](./tests/rbac-allowed.test.ts)

## Implementation

[@generates](./src/auth-plugin.ts)

## API

```typescript { #api }
import { FastifyInstance, FastifyRequest } from 'fastify'

export interface JwtPayload {
  sub: string
  role: string
  iat: number
  exp: number
}

declare module 'fastify' {
  interface FastifyRequest {
    user: JwtPayload
  }
}

export async function authPlugin(app: FastifyInstance): Promise<void>
export function requireRole(role: string): (request: FastifyRequest) => Promise<void>
```

## Dependencies { .dependencies }

### fastify 1.0.0 { .dependency }

Fast and low overhead web framework for Node.js. Provides hooks (onRequest/preHandler), decorators, and TypeScript request augmentation used for attaching user context.

[@satisfied-by](fastify)

### @matteo.collina/skills 1.0.0 { .dependency }

Provides Fastify authentication skill definitions including hook-based JWT verification patterns and TypeScript request augmentation.

[@satisfied-by](@matteo.collina/skills)
