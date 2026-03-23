# @matteo.collina/skills

`@matteo.collina/skills` is a curated collection of AI-ready skill definitions for Node.js/TypeScript development. Each skill is a structured Markdown document (with optional sub-rules) that an AI agent loads to gain specialized knowledge in a particular domain. The package also exports a minimal TypeScript entry point.

## Package Information

- **Package Name**: `@matteo.collina/skills`
- **Package Type**: npm
- **Language**: TypeScript
- **Version**: 1.0.0
- **Installation**: `npm install @matteo.collina/skills`

## Core Imports

```typescript
import { version } from '@matteo.collina/skills';
```

CommonJS:

```javascript
const { version } = require('@matteo.collina/skills');
```

> **Note for source installations**: The package source repository does not include
> a compiled build or a `main`/`exports` field in its `package.json`. When installing
> from source, import directly from the TypeScript source file:
>
> ```typescript
> // With --experimental-strip-types (Node.js v22+)
> import { version } from '@matteo.collina/skills/src/index.ts';
> ```
>
> For a published npm release, the standard import above works as expected.

## TypeScript API

```typescript { .api }
/**
 * The package version string.
 */
export const version: string;
```

## Basic Usage

The primary way to use this package is to load skill Markdown files into an AI assistant context. The files are located in the `skills/` directory of the installed package.

```typescript
import { version } from '@matteo.collina/skills';
import { readFileSync } from 'node:fs';
import { resolve, dirname } from 'node:path';
import { createRequire } from 'node:module';

// Resolve the package root (reliable method via package.json)
const require = createRequire(import.meta.url);
const pkgRoot = resolve(dirname(require.resolve('@matteo.collina/skills/package.json')));

// Load a skill entry point
const fastifySkill = readFileSync(
  resolve(pkgRoot, 'skills/fastify/SKILL.md'),
  'utf-8'
);
```

## Skill Architecture

Each skill is organized as:

```
skills/<skill-name>/
  SKILL.md         # Entry point: metadata frontmatter + instructions + rule links
  tile.json        # Tile manifest (most skills; skill-optimizer has none)
  rules/           # Optional sub-documents for detailed guidance
    <topic>.md
```

**SKILL.md frontmatter format** (all skills):

```yaml { .api }
---
name: <skill-name>
description: "<activation description used by AI systems>"
metadata:
  tags: <comma-separated tags>
---
```

**tile.json format** (per-skill manifests):

```json { .api }
{
  "name": "<registry>/<tile-name>",
  "version": "<semver>",
  "private": false,
  "summary": "<one-sentence description>",
  "skills": {
    "<skill-name>": {
      "path": "SKILL.md"
    }
  }
}
```

## Capabilities

### documentation skill

Creates, structures, and reviews technical documentation following the Diátaxis framework (tutorials, how-to guides, reference, explanation).

```text { .api }
Path:  skills/documentation/SKILL.md
Tile:  skills/documentation/tile.json
Name:  mcollina/documentation  (v0.1.0)
Tags:  documentation, technical-writing, tutorials, guides, reference, diataxis
```

[Documentation Skill](./skills/documentation.md)

### fastify skill

Guides development of Fastify Node.js backend servers and REST APIs using TypeScript or JavaScript. Covers routes, plugins, schemas, hooks, authentication, CORS, databases, WebSockets, and deployment.

```text { .api }
Path:   skills/fastify/SKILL.md
Rules:  skills/fastify/rules/  (19 rule files)
Tile:   skills/fastify/tile.json
Name:   mcollina/fastify-best-practices  (v0.1.0)
Tags:   fastify, nodejs, typescript, backend, api, server, http
```

[Fastify Skill](./skills/fastify.md)

### init skill

Creates, updates, or optimizes an AGENTS.md file with minimal, high-signal, non-discoverable coding conventions for AI agents.

```text { .api }
Path:  skills/init/SKILL.md
Tile:  skills/init/tile.json
Name:  mcollina/init  (v0.1.0)
Tags:  initialization, agents, context-engineering, agents-md, maintenance
```

[Init Skill](./skills/init.md)

### linting-neostandard-eslint9 skill

Configures ESLint v9 flat config and neostandard for JavaScript/TypeScript projects, including migration from legacy `.eslintrc*` or `standard`.

```text { .api }
Path:   skills/linting-neostandard-eslint9/SKILL.md
Rules:  skills/linting-neostandard-eslint9/rules/  (5 rule files)
Tile:   skills/linting-neostandard-eslint9/tile.json
Name:   mcollina/linting-neostandard-eslint9  (v0.1.0)
Tags:   linting, neostandard, eslint, eslint9, flat-config, javascript, typescript
```

[Linting Skill](./skills/linting-neostandard-eslint9.md)

### node skill

Best practices for Node.js development with TypeScript — type stripping, async patterns, error handling, streams, testing, caching, logging, and more.

```text { .api }
Path:   skills/node/SKILL.md
Rules:  skills/node/rules/  (15 rule files + assets/)
Tile:   skills/node/tile.json
Name:   mcollina/node-best-practices  (v0.1.0)
Tags:   node, nodejs, javascript, typescript, type-stripping, backend, server
```

[Node Skill](./skills/node.md)

### nodejs-core skill

Deep Node.js internals expertise: V8 engine, libuv event loop, N-API, C++ addons, native memory, and build systems.

```text { .api }
Path:   skills/nodejs-core/SKILL.md
Rules:  skills/nodejs-core/rules/  (22 rule files)
Tile:   skills/nodejs-core/tile.json
Name:   mcollina/nodejs-core  (v0.1.0)
Tags:   nodejs, v8, libuv, cpp, native-addons, performance, debugging, internals
```

[Node.js Core Skill](./skills/nodejs-core.md)

### oauth skill

Implements OAuth 2.0/2.1 authorization flows in Fastify: authorization code + PKCE, client credentials, device flow, refresh token rotation, JWT validation, token introspection/revocation.

```text { .api }
Path:  skills/oauth/SKILL.md
Tile:  skills/oauth/tile.json
Name:  mcollina/oauth  (v0.1.0)
Tags:  oauth, oauth2, security, authentication, authorization, jwt, fastify
```

[OAuth Skill](./skills/oauth.md)

### octocat skill

Handles git and GitHub operations using the gh CLI — PRs, issues, branching, merging, rebasing, history rewriting, GitHub Actions, releases.

```text { .api }
Path:  skills/octocat/SKILL.md
Tile:  skills/octocat/tile.json
Name:  mcollina/octocat  (v0.1.0)
Tags:  git, github, gh-cli, version-control, merge-conflicts, pull-requests
```

[Octocat Skill](./skills/octocat.md)

### skill-optimizer skill

Optimizes AI skills for activation, clarity, and cross-model reliability — benchmarking, activation design, context budget, regression triage, release gates.

```text { .api }
Path:   skills/skill-optimizer/SKILL.md
Rules:  skills/skill-optimizer/rules/  (5 rule files)
Note:   No tile.json
Tags:   skills, optimization, benchmarking, activation, regressions, prompt-engineering
```

[Skill Optimizer Skill](./skills/skill-optimizer.md)

### snipgrapher skill

Configures and uses snipgrapher to generate polished code snippet images (PNG, SVG, WebP) with custom themes, profiles, and styling options.

```text { .api }
Path:   skills/snipgrapher/SKILL.md
Rules:  skills/snipgrapher/rules/  (2 rule files)
Tile:   skills/snipgrapher/tile.json
Name:   mcollina/snipgrapher  (v0.1.0)
Tags:   snipgrapher, snippets, images, svg, png, webp, cli
```

[Snipgrapher Skill](./skills/snipgrapher.md)

### typescript-magician skill

Designs complex generic types, refactors `any` types, creates type guards and utility types, and resolves TypeScript compiler errors.

```text { .api }
Path:   skills/typescript-magician/SKILL.md
Rules:  skills/typescript-magician/rules/  (14 rule files)
Tile:   skills/typescript-magician/tile.json
Name:   mcollina/typescript-magician  (v0.1.0)
Tags:   typescript, types, generics, type-safety, advanced-typescript
```

[TypeScript Magician Skill](./skills/typescript-magician.md)
