# Health-Check HTTP Server

A production-ready HTTP server that exposes a single health-check endpoint and starts cleanly with structured logging.

## Capabilities

### Serve a health endpoint

- `GET /health` returns HTTP 200 with JSON body `{ "status": "ok" }` [@test](./tests/health.test.ts)
- `GET /health` responds with `Content-Type: application/json` [@test](./tests/health-content-type.test.ts)

### Structured logging

- The server starts with the built-in Pino logger enabled at the `info` level [@test](./tests/logging.test.ts)

### Async start and graceful error handling

- The server binds to port 3000 on host `0.0.0.0` using an async start function [@test](./tests/startup.test.ts)
- If `listen()` throws (e.g. port in use), the error is logged and the process exits with code 1 [@test](./tests/startup-error.test.ts)

## Implementation

[@generates](./src/server.ts)

## API

```typescript { #api }
export async function startServer(): Promise<void>
```

## Dependencies { .dependencies }

### fastify 1.0.0 { .dependency }

Fast and low overhead web framework for Node.js. Provides the server factory, route registration, built-in Pino logger integration, and the `listen()` method.

[@satisfied-by](fastify)
