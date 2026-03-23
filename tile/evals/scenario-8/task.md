# ESLint v9 Flat Configuration

Set up ESLint v9 using the flat configuration format with the neostandard preset for a mixed JavaScript and TypeScript project.

## Capabilities

### Create a valid ESLint v9 flat config

- `eslint.config.mjs` exports a default array that ESLint v9 recognizes as a flat config [@test](./tests/config-loads.test.ts)
- Running `eslint .` with the config produces no crash or "configuration not found" error [@test](./tests/eslint-runs.test.ts)

### Apply neostandard rules

- A file that violates a neostandard rule (e.g. uses `var` instead of `let`/`const`) is reported as an error [@test](./tests/neostandard-rule.test.ts)
- A file that conforms to neostandard passes without errors [@test](./tests/neostandard-pass.test.ts)

### TypeScript file support

- `.ts` files are linted with TypeScript-aware rules enabled [@test](./tests/typescript-files.test.ts)

### Ignore patterns

- Files matching `dist/**` and `node_modules/**` are excluded from linting [@test](./tests/ignore-patterns.test.ts)

## Implementation

[@generates](./eslint.config.mjs)

## API

```javascript { #api }
// eslint.config.mjs — no programmatic exports; evaluated by ESLint CLI
export default [
  // array of flat config objects
]
```

## Dependencies { .dependencies }

### @matteo.collina/skills 1.0.0 { .dependency }

Provides ESLint v9 flat config and neostandard skill definitions including setup, TypeScript integration, and CI guidance.

[@satisfied-by](@matteo.collina/skills)
