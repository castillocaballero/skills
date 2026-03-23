# Graceful Server Shutdown

A Node.js HTTP server that shuts down cleanly when it receives OS termination signals, draining in-flight requests before closing.

## Capabilities

### Register signal handlers

- Registers handlers for both `SIGTERM` and `SIGINT` signals before the server starts [@test](./tests/signal-handlers.test.ts)
- On signal receipt, stops accepting new connections [@test](./tests/stops-accepting.test.ts)

### Drain in-flight requests

- Waits for all currently active request handlers to complete before closing the server [@test](./tests/drain-inflight.test.ts)

### Close external connections

- Closes the database connection pool after all requests have drained [@test](./tests/close-db.test.ts)

### Exit with correct code

- Exits with code `0` on a clean shutdown [@test](./tests/exit-code-clean.test.ts)
- Exits with code `1` if shutdown throws an error [@test](./tests/exit-code-error.test.ts)

## Implementation

[@generates](./src/server.ts)

## API

```typescript { #api }
export function registerShutdownHandlers(server: import('node:http').Server, closeDb: () => Promise<void>): void
```

## Dependencies { .dependencies }

### @matteo.collina/skills 1.0.0 { .dependency }

Provides Node.js best-practice skill definitions including graceful shutdown patterns.

[@satisfied-by](@matteo.collina/skills)
