# Fastify Route Integration Tests

A test suite that verifies the behaviour of a small Fastify HTTP API without starting a real network server.

## Capabilities

### Test a JSON GET endpoint

Given a Fastify application that exposes `GET /items` and returns `{ items: [] }` when no items exist:

- A `GET /items` request returns HTTP 200 with body `{ "items": [] }` [@test](./tests/get-items.test.ts)
- A `GET /items` request returns the correct `Content-Type: application/json` header [@test](./tests/get-items-content-type.test.ts)

### Test a POST endpoint with request body validation

Given a Fastify application that exposes `POST /items` accepting `{ name: string }` and returning `{ id: number, name: string }`:

- A `POST /items` with body `{ "name": "apple" }` returns HTTP 201 and a response body containing the submitted name [@test](./tests/post-item.test.ts)
- A `POST /items` with a missing `name` field returns HTTP 400 [@test](./tests/post-item-validation.test.ts)

### Test application lifecycle

- The application can be built, used for injection, and then closed without errors [@test](./tests/lifecycle.test.ts)

## Implementation

[@generates](./src/app.ts)

## API

```typescript { #api }
import Fastify, { FastifyInstance } from 'fastify'

export function buildApp(): FastifyInstance
```

## Dependencies { .dependencies }

### fastify 5 { .dependency }

Fast and low overhead web framework for Node.js. Provides the `Fastify()` factory, `FastifyInstance`, `inject()` for in-process HTTP testing, and route registration methods.

[@satisfied-by](fastify)
