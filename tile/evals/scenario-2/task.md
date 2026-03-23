# User Registration API with Schema Validation

A Fastify route that validates incoming user registration requests and serializes responses, using JSON Schema definitions attached directly to the route.

## Capabilities

### Validate the request body

- A `POST /users` request with `{ "username": "alice", "email": "alice@example.com", "age": 25 }` returns HTTP 201 and echoes the submitted fields [@test](./tests/create-user.test.ts)
- A `POST /users` request missing the required `email` field returns HTTP 400 [@test](./tests/missing-email.test.ts)
- A `POST /users` request with `age` as a string (e.g. `"age": "twenty"`) returns HTTP 400 [@test](./tests/invalid-age.test.ts)

### Validate query parameters

- A `GET /users` request with `?page=2&limit=10` returns HTTP 200 [@test](./tests/list-users-query.test.ts)
- A `GET /users` request with `?page=abc` returns HTTP 400 [@test](./tests/invalid-query.test.ts)

### Serialize responses with a strict schema

- The `POST /users` 201 response body contains exactly the fields `id`, `username`, and `email` — additional internal fields are stripped [@test](./tests/response-serialization.test.ts)

## Implementation

[@generates](./src/routes/users.ts)

## API

```typescript { #api }
import { FastifyInstance } from 'fastify'

export async function usersRoutes(app: FastifyInstance): Promise<void>
```

## Dependencies { .dependencies }

### fastify 1.0.0 { .dependency }

Fast and low overhead web framework for Node.js. Provides schema-based request validation and response serialization via JSON Schema attached to route options.

[@satisfied-by](fastify)
