# Linting (neostandard + ESLint 9) Skill

The `linting-neostandard-eslint9` skill configures ESLint v9 flat config and neostandard for JavaScript and TypeScript projects, including migrating from legacy `.eslintrc*` files or the `standard` package.

## Skill Reference

```text { .api }
Skill name:  linting-neostandard-eslint9
Tile name:   mcollina/linting-neostandard-eslint9
Version:     0.1.0
Path:        skills/linting-neostandard-eslint9/SKILL.md
Tile:        skills/linting-neostandard-eslint9/tile.json
Tags:        linting, neostandard, eslint, eslint9, flat-config, javascript, typescript
```

## When to Use

- Setting up linting in a JavaScript or TypeScript project
- Using neostandard as a Standard-like ESLint v9 flat-config baseline
- Configuring ESLint v9 with the flat config system (`eslint.config.js`/`.mjs`)
- Migrating from `standard` to `neostandard` or ESLint v9
- Migrating from legacy `.eslintrc*` configuration to ESLint v9
- Running linting consistently in CI and local development

## Rule Sub-Documents

```text { .api }
skills/linting-neostandard-eslint9/rules/
  ci-and-editor-integration.md      - CI pipelines and VSCode editor integration
  eslint-v9-flat-config.md          - ESLint v9 flat config system details
  migration-from-legacy-eslint.md   - Migration from legacy .eslintrc* to flat config
  migration-from-standard.md        - Migration from `standard` package to neostandard
  neostandard.md                    - neostandard configuration and rule customization
```

## Quick Start

```bash
npm install --save-dev eslint@9 neostandard
```

```javascript { .api }
// eslint.config.js (or eslint.config.mjs)
import neostandard from 'neostandard'

export default neostandard()
```

## Key Capabilities

### Basic neostandard Setup

```javascript { .api }
// eslint.config.mjs — minimal setup
import neostandard from 'neostandard'

export default neostandard()
```

### neostandard with TypeScript

```javascript { .api }
// eslint.config.mjs — TypeScript project
import neostandard from 'neostandard'

export default neostandard({ ts: true })
```

### Adding Custom Rules on Top of neostandard

```javascript { .api }
// eslint.config.mjs — custom overrides
import neostandard from 'neostandard'

export default [
  ...neostandard({ ts: true }),
  {
    rules: {
      'no-console': 'warn',
      '@typescript-eslint/no-explicit-any': 'error'
    }
  }
]
```

### Ignoring Files

```javascript { .api }
// eslint.config.mjs
import neostandard from 'neostandard'

export default [
  { ignores: ['dist/', 'coverage/', 'node_modules/'] },
  ...neostandard({ ts: true })
]
```

### ESLint v9 Flat Config File Locations

```text { .api }
Supported flat config filenames (ESLint v9):
  eslint.config.js     - CJS or ESM (depends on package.json "type")
  eslint.config.mjs    - Always ESM
  eslint.config.cjs    - Always CJS
```

### Common Workflow

```text { .api }
1. Install eslint@9 and neostandard (see Quick Start)
2. Create eslint.config.js with neostandard() as the base
3. Add project-specific rule overrides on top
4. Run: npx eslint .
5. Add lint script to package.json: "lint": "eslint ."
6. Integrate into CI with non-fix run; use --fix only locally
```

### CI Integration

```yaml { .api }
# .github/workflows/ci.yml (example)
- name: Lint
  run: npm run lint
  # Never use --fix in CI; only audit
```

### Editor Integration (VSCode)

```json { .api }
// .vscode/settings.json
{
  "eslint.useFlatConfig": true,
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": "explicit"
  }
}
```

## Tile Manifest

```json { .api }
// skills/linting-neostandard-eslint9/tile.json
{
  "name": "mcollina/linting-neostandard-eslint9",
  "version": "0.1.0",
  "private": false,
  "summary": "Configures ESLint v9 flat config and neostandard for JavaScript and TypeScript projects.",
  "skills": {
    "linting-neostandard-eslint9": {
      "path": "SKILL.md"
    }
  }
}
```
