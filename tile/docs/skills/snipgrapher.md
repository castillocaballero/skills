# Snipgrapher Skill

The `snipgrapher` skill configures and uses snipgrapher to generate polished code snippet images (PNG, SVG, WebP) with custom themes, profiles, and styling options.

## Skill Reference

```text { .api }
Skill name:  snipgrapher
Tile name:   mcollina/snipgrapher
Version:     0.1.0
Path:        skills/snipgrapher/SKILL.md
Tile:        skills/snipgrapher/tile.json
Tags:        snipgrapher, snippets, images, svg, png, webp, cli
```

## When to Use

- Generating image snippets from source code
- Configuring reusable snippet rendering defaults (named profiles)
- Batch-rendering snippet assets for docs, social posts, or changelogs
- Creating syntax-highlighted PNGs/SVGs from source files

**Trigger terms**: code screenshots, code images, shareable code snippets, pretty code images, syntax-highlighted images, snipgrapher

## Rule Sub-Documents

```text { .api }
skills/snipgrapher/rules/
  setup-and-configuration.md  - Install, select executable, initialize config, define profiles
  rendering-workflows.md      - Single renders, batch jobs, watch mode, output practices
```

## Key Capabilities

### Quick Start (No Config)

```bash { .api }
# Render a single file to PNG immediately — no config required
npx snipgrapher render file.ts -o output.png
```

### Initializing Project Config

```bash { .api }
# Initialize a project config file (creates snipgrapher.config.json)
npx snipgrapher init
```

### Render with Profile

```bash { .api }
npx snipgrapher render file.ts --profile default -o output.png
```

### Verifying Output

```bash { .api }
# Always verify the output after rendering
ls -lh output.png   # confirm file exists and size > 0
```

### Supported Output Formats

```text { .api }
-o output.png     # PNG (raster, default)
-o output.svg     # SVG (vector)
-o output.webp    # WebP (compressed raster)
```

### Configuration File

```json { .api }
// snipgrapher.config.json (created by `snipgrapher init`)
{
  "profiles": {
    "default": {
      "theme": "nord",
      "font": "JetBrains Mono",
      "fontSize": 14,
      "padding": 20,
      "background": "#2e3440",
      "showLineNumbers": true,
      "windowStyle": "mac"
    },
    "social": {
      "theme": "dracula",
      "fontSize": 16,
      "padding": 40,
      "showLineNumbers": false
    }
  }
}
```

### Batch Rendering

```bash { .api }
# Render multiple files
npx snipgrapher render src/*.ts -o dist/snippets/

# Render with a specific profile in batch
npx snipgrapher render src/*.ts --profile social -o dist/snippets/
```

### Watch Mode

```bash { .api }
# Watch a file and re-render on changes
npx snipgrapher watch file.ts --profile default -o output.png
```

### Using Installed Binary vs npx

```bash { .api }
# Installed locally:
./node_modules/.bin/snipgrapher render file.ts -o out.png

# Via npx (always latest or specified version):
npx snipgrapher render file.ts -o out.png
npx snipgrapher@1.2.3 render file.ts -o out.png   # pin version
```

## Core Principles

```text { .api }
- Configure first: establish project config before repeated renders
- Reproducible output: prefer named profiles and explicit output paths
- Portable commands: use patterns that work with installed binaries and npx
- Automation-friendly: rely on CLI flags/config/env precedence intentionally
- Always verify output: check ls -lh after rendering to confirm success
```

## Tile Manifest

```json { .api }
// skills/snipgrapher/tile.json
{
  "name": "mcollina/snipgrapher",
  "version": "0.1.0",
  "private": false,
  "summary": "Configures and uses snipgrapher to generate polished code snippet images.",
  "skills": {
    "snipgrapher": {
      "path": "SKILL.md"
    }
  }
}
```
