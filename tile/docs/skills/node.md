# Node.js Best Practices Skill

The `node` skill provides domain-specific best practices for Node.js development with TypeScript, covering type stripping, async patterns, error handling, streams, modules, testing, performance, caching, logging, and more.

## Skill Reference

```text { .api }
Skill name:  node (tile: node-best-practices)
Tile name:   mcollina/node-best-practices
Version:     0.1.0
Path:        skills/node/SKILL.md
Tile:        skills/node/tile.json
Tags:        node, nodejs, javascript, typescript, type-stripping, backend, server
```

## When to Use

- Setting up Node.js projects with native TypeScript support
- Configuring type stripping (`--experimental-strip-types`)
- Writing Node 22+ TypeScript without a build step
- Implementing graceful shutdown, error handling, or flaky test diagnosis
- Profiling or caching strategies in Node.js

**Trigger terms**: native TypeScript in Node, strip types, Node 22 TypeScript, .ts files without compilation, ts-node alternative

## Rule Sub-Documents

```text { .api }
skills/node/rules/
  async-patterns.md             - Async/await patterns, Promise.all, error propagation
  caching.md                    - In-memory, Redis, and HTTP caching strategies
  environment.md                - Environment variable loading (.env, dotenv, @fastify/env)
  error-handling.md             - Error classification (operational vs programmer), base classes
  flaky-tests.md                - Diagnosing and fixing non-deterministic test failures
  graceful-shutdown.md          - SIGTERM/SIGINT handlers, drain in-flight requests
  logging.md                    - Structured logging with Pino
  modules.md                    - ESM/CJS interop, import extensions, package.json "type"
  node-modules-exploration.md   - Exploring installed packages in node_modules
  performance.md                - Event loop health, profiling, avoiding blocking calls
  profiling.md                  - CPU and heap profiling with --prof, --heap-prof
  streams.md                    - Readable, Writable, Transform, pipeline()
  stuck-processes-and-tests.md  - Diagnosing hangs, open handles, CI timeouts
  testing.md                    - Node built-in test runner (node:test)
  typescript.md                 - Type stripping configuration and tsconfig.json

skills/node/rules/assets/
  graceful-server.ts            - Reference implementation of graceful-shutdown server
  graceful-server.test.ts       - Tests for the graceful-shutdown reference implementation
```

## Key Capabilities

### TypeScript with Type Stripping (Node 22.6+)

Run TypeScript directly without build tools by removing type annotations at runtime.

```text { .api }
Requirements for type stripping compatibility:
- Use `import type` for type-only imports
- Use const objects instead of enums
- Avoid namespaces and parameter properties
- Use .ts extensions in imports

Run with:
  node greet.ts
  node --experimental-strip-types greet.ts  (older Node 22 versions)
```

```typescript { .api }
// greet.ts — valid type-stripped TypeScript
import type { IncomingMessage } from 'node:http'

const greet = (name: string): string => `Hello, ${name}!`
console.log(greet('world'))
```

### Graceful Shutdown

```text { .api }
Pattern:
1. Register signal handlers (SIGTERM/SIGINT)
2. Stop accepting new work
3. Drain in-flight requests
4. Close external connections (DB, cache)
5. Exit with appropriate code

See: rules/graceful-shutdown.md and rules/assets/graceful-server.ts
```

### Error Handling

```text { .api }
Pattern:
1. Define a shared error base class
2. Classify errors: operational (expected) vs programmer (bugs)
3. Add async boundary handlers: process.on('unhandledRejection')
4. Propagate typed errors through the call stack
5. Log with context before responding or crashing

See: rules/error-handling.md
```

### Diagnosing Flaky Tests

```text { .api }
Pattern:
1. Isolate the test with --test-only
2. Check for shared state or timer dependencies
3. Inspect async teardown order
4. Add retry logic as a temporary diagnostic step
5. Fix root cause

See: rules/flaky-tests.md
```

### Diagnosing Stuck Processes/Tests

```text { .api }
Symptoms: node --test hangs, "process did not exit", CI timeout, open handles

Pattern:
1. Isolate file/test
2. Run with explicit timeout/reporter
3. Inspect handles via why-is-node-running (SIGUSR1)
4. Patch deterministic teardown in resource-creation scope
5. Rerun isolated + full suite until stable

See: rules/stuck-processes-and-tests.md
```

### Node Built-in Test Runner

```typescript { .api }
import { test, describe, it, before, after, beforeEach, afterEach } from 'node:test'
import assert from 'node:assert/strict'

test('basic test', async () => {
  assert.equal(1 + 1, 2)
})

describe('suite', () => {
  it('sub-test', () => {
    assert.ok(true)
  })
})
```

Run:
```bash
node --test                          # all test files
node --test path/to/file.test.ts     # single file
node --test --test-name-pattern "foo" # filter by name
```

### Streams

```typescript { .api }
import { pipeline } from 'node:stream/promises'
import { createReadStream, createWriteStream } from 'node:fs'
import { createGzip } from 'node:zlib'

// Promisified pipeline — handles backpressure and cleanup automatically
await pipeline(
  createReadStream('input.txt'),
  createGzip(),
  createWriteStream('output.txt.gz')
)
```

### High-Priority Activation Checklist (Streams + Caching)

When the task mentions **CSV**, **ETL**, **ingestion pipelines**, **large file processing**, **backpressure**, **repeated lookups**, or **deduplicating concurrent async calls**, apply this checklist:

```text { .api }
1. Use `await pipeline(...)` from `node:stream/promises`
   (prefer over chained .pipe() calls)
2. Include at least one explicit `async function*` transform when
   data is being transformed in-stream
3. Choose a cache strategy when repeated work appears:
   - `lru-cache` for bounded in-memory reuse in a single process
   - `async-cache-dedupe` for async request deduplication / stale-while-revalidate
4. Show where backpressure is handled (implicitly via pipeline()
   or explicitly via drain)

Integrated example structure for CSV/ETL prompts:
  createReadStream(input)
  → async function* parser/transform
  → optional cached enrichment lookup (async-cache-dedupe or lru-cache)
  → await pipeline(...) to a writable destination

See: rules/streams.md, rules/caching.md
```

### Profiling a Slow Path

```text { .api }
Pattern:
1. Reproduce under realistic load
2. Capture CPU profile with --cpu-prof
3. Identify hot functions
4. Check for stream backpressure or unnecessary serialisation
5. Validate improvement with a benchmark

See: rules/profiling.md, rules/performance.md
```

## Tile Manifest

```json { .api }
// skills/node/tile.json
{
  "name": "mcollina/node-best-practices",
  "version": "0.1.0",
  "private": false,
  "summary": "Provides domain-specific best practices for Node.js development with TypeScript.",
  "skills": {
    "node-best-practices": {
      "path": "SKILL.md"
    }
  }
}
```
