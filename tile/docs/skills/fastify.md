# Fastify Skill

The `fastify` skill guides development of Fastify Node.js backend servers and REST APIs using TypeScript or JavaScript. It covers the full Fastify request lifecycle and provides best practices via 19 specialized rule sub-documents.

## Skill Reference

```text { .api }
Skill name:  fastify-best-practices
Tile name:   mcollina/fastify-best-practices
Version:     0.1.0
Path:        skills/fastify/SKILL.md
Tile:        skills/fastify/tile.json
Tags:        fastify, nodejs, typescript, backend, api, server, http
```

## When to Use

- Building or configuring Fastify applications
- Defining routes, plugins, and JSON Schema validation
- Handling errors, optimizing performance, managing authentication
- Configuring CORS and security headers
- Integrating databases and WebSockets
- Deploying Fastify apps to production

**Trigger terms**: Fastify, Node.js server, REST API, API routes, backend framework, fastify.config, server.ts, app.ts

## Recommended Reading Order

- **New to Fastify**: `plugins.md` → `routes.md` → `schemas.md`
- **Adding authentication**: `plugins.md` → `hooks.md` → `authentication.md`
- **Improving performance**: `schemas.md` → `serialization.md` → `performance.md`
- **Setting up testing**: `routes.md` → `testing.md`
- **Going to production**: `logging.md` → `configuration.md` → `deployment.md`

## Quick Start

```typescript
import Fastify from 'fastify'

const app = Fastify({ logger: true })

app.get('/health', async (request, reply) => {
  return { status: 'ok' }
})

const start = async () => {
  await app.listen({ port: 3000, host: '0.0.0.0' })
}
start()
```

## Rule Sub-Documents

```text { .api }
skills/fastify/rules/
  authentication.md     - JWT/session-based authentication patterns
  configuration.md      - App configuration with env vars and plugins
  content-type.md       - Content-type parsing and multipart handling
  cors-security.md      - CORS headers and security hardening
  database.md           - Database integration (pg, mysql2, etc.)
  decorators.md         - Fastify decorators for extending request/reply/instance
  deployment.md         - Production deployment (Docker, process managers)
  error-handling.md     - Custom error handlers, HTTP errors, validation errors
  hooks.md              - Lifecycle hooks (onRequest, preHandler, onSend, etc.)
  http-proxy.md         - HTTP reverse proxy with @fastify/http-proxy
  logging.md            - Structured logging with Pino
  performance.md        - Schema-based serialization, connection keep-alive
  plugins.md            - Plugin encapsulation with fastify-plugin
  routes.md             - Route definition, parameters, query strings
  schemas.md            - JSON Schema validation for request/response
  serialization.md      - Fast-json-stringify and custom serializers
  testing.md            - Injection-based testing with app.inject()
  typescript.md         - TypeScript with strip types (no build step)
  websockets.md         - WebSocket support with @fastify/websocket
```

## Key Capabilities

### Plugin Development

Plugins are the primary encapsulation mechanism in Fastify.

```typescript { .api }
// Encapsulated plugin (using fastify-plugin to share scope)
import fp from 'fastify-plugin'
import { FastifyInstance } from 'fastify'

async function myPlugin(fastify: FastifyInstance, options: Record<string, unknown>) {
  fastify.decorate('myUtil', () => { /* ... */ })
}

export default fp(myPlugin)
```

### Route Definition

```typescript { .api }
// GET with typed params and query
fastify.get<{
  Params: { id: string }
  Querystring: { include?: string }
}>('/items/:id', {
  schema: {
    params: { type: 'object', properties: { id: { type: 'string' } }, required: ['id'] },
    response: { 200: { type: 'object', properties: { id: { type: 'string' } } } }
  }
}, async (request, reply) => {
  const { id } = request.params
  return { id }
})
```

### JSON Schema Validation

```typescript { .api }
const schema = {
  body: {
    type: 'object',
    required: ['name', 'email'],
    properties: {
      name: { type: 'string', minLength: 1 },
      email: { type: 'string', format: 'email' }
    }
  },
  response: {
    200: {
      type: 'object',
      properties: {
        id: { type: 'integer' },
        name: { type: 'string' }
      }
    }
  }
}
```

### Lifecycle Hooks

```typescript { .api }
// Hook execution order (request lifecycle):
// onRequest → preParsing → preValidation → preHandler → handler → preSerialization → onSend → onResponse

fastify.addHook('onRequest', async (request, reply) => { /* auth check */ })
fastify.addHook('preHandler', async (request, reply) => { /* business logic */ })
fastify.addHook('onSend', async (request, reply, payload) => { return payload })
fastify.addHook('onError', async (request, reply, error) => { /* error logging */ })
```

### Testing with inject()

```typescript { .api }
import { build } from './app.js'

const app = await build()

const response = await app.inject({
  method: 'POST',
  url: '/users',
  payload: { name: 'Alice', email: 'alice@example.com' }
})

// response.statusCode: number
// response.json(): parsed JSON body
// response.headers: response headers
```

### Error Handling

```typescript { .api }
import createError from '@fastify/error'

const NotFoundError = createError('NOT_FOUND', 'Resource not found', 404)

fastify.setErrorHandler(async (error, request, reply) => {
  reply.status(error.statusCode ?? 500).send({
    error: error.name,
    message: error.message
  })
})
```

## Tile Manifest

```json { .api }
// skills/fastify/tile.json
{
  "name": "mcollina/fastify-best-practices",
  "version": "0.1.0",
  "private": false,
  "summary": "Guides development of Fastify Node.js backend servers and REST APIs using TypeScript or JavaScript.",
  "skills": {
    "fastify-best-practices": {
      "path": "SKILL.md"
    }
  }
}
```
