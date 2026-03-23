# Fastify Plugin System

A modular Fastify application split into two independently-encapsulated feature plugins, composed into a root application builder.

## Capabilities

### Scoped plugin with its own routes

- A `products` plugin registers `GET /products` and returns an empty array; the route is inaccessible from outside its scope unless explicitly exported [@test](./tests/products-route.test.ts)
- A `users` plugin registers `GET /users` and returns an empty array independently of the products plugin [@test](./tests/users-route.test.ts)

### Plugin registration with options

- Each plugin accepts an `options` object and uses an option value in its response (e.g. `prefix`) [@test](./tests/plugin-options.test.ts)

### Breaking encapsulation to share a decorator

- A shared decorator (e.g. `app.config`) is registered once using the correct approach that makes it available in all child plugins [@test](./tests/shared-decorator.test.ts)

### Prefix-based route mounting

- Registering the products plugin with `{ prefix: '/api/v1' }` makes routes available at `/api/v1/products` [@test](./tests/prefix-mounting.test.ts)

## Implementation

[@generates](./src/app.ts)

## API

```typescript { #api }
import { FastifyInstance, FastifyPluginAsync } from 'fastify'

export const productsPlugin: FastifyPluginAsync
export const usersPlugin: FastifyPluginAsync
export function buildApp(): FastifyInstance
```

## Dependencies { .dependencies }

### fastify 1.0.0 { .dependency }

Fast and low overhead web framework for Node.js. Provides the plugin encapsulation model, register(), and the fastify-plugin utility for breaking encapsulation.

[@satisfied-by](fastify)
