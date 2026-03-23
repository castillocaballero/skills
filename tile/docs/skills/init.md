# Init Skill

The `init` skill creates, updates, or optimizes an `AGENTS.md` file for a repository. It focuses on minimal, high-signal instructions covering non-discoverable coding conventions, tooling quirks, workflow preferences, and project-specific rules that AI agents cannot infer from reading the codebase.

## Skill Reference

```text { .api }
Skill name:  init
Tile name:   mcollina/init
Version:     0.1.0
Path:        skills/init/SKILL.md
Tile:        skills/init/tile.json
Tags:        initialization, agents, context-engineering, agents-md, maintenance
```

## When to Use

- Setting up agent instructions or Claude configuration for a new repository
- When an existing `AGENTS.md` is too long, generic, or stale
- When agents repeatedly make avoidable mistakes
- When repository workflows have changed and agent guidance needs pruning

## Capabilities

### Core Rule: Discoverability Filter

```text { .api }
Before adding any line, ask:
  "Can an agent discover this by reading the repo (README, code, config, scripts, directory tree)?"

If YES:  do NOT include it in AGENTS.md
If NO, and it materially affects task success/cost/safety: include it
```

### What Earns a Line

```text { .api }
Include only guidance that is ALL THREE of:
1. Non-discoverable from repository files alone
2. Operationally significant (changes commands, outcomes, or safety)
3. Actionable (specific enough to execute)

Typical examples:
- Non-standard tooling choices (e.g., use `uv` instead of `pip`)
- Command caveats (e.g., tests must run with `--no-cache` due to fixture behavior)
- Hidden constraints/landmines (deprecated directories still imported in production)
- Critical local conventions not encoded in lint/tests/config
```

### What to Exclude

```text { .api }
Do NOT include:
- Tech stack summaries
- Directory structure overviews
- Architecture descriptions agents can infer from code
- Generic best-practice advice
- Rules already enforced by tooling (linters, typecheck, tests, CI)
```

### Quality Gate (per line)

```text { .api }
Before finalizing each line, verify:
1. Is this still accurate for the current state of the repo?
2. Would omitting it cause an agent to fail or make a costly mistake?
3. Is it specific enough to act on without ambiguity?

If any answer is NO → remove or rewrite the line
```

### AGENTS.md Structure

```text { .api }
Recommended structure (keep concise):
- Scope & routing: which areas need separate/module-local AGENTS files
- Non-discoverable commands: build/test/lint invocations with non-obvious flags
- Landmines / do-not-touch areas: deprecated directories, critical constraints
- Task-specific constraints: things agents must NOT do
```

### Source Files to Check First

```text { .api }
Before creating or updating AGENTS.md, review these files:
- Existing AGENTS.md (improve incrementally, don't replace blindly)
- README.md
- PROJECT.md (if present)
- Cursor rules (.cursor/rules/ or .cursorrules)
- Copilot instructions (.github/copilot-instructions.md)
- GEMINI.md
- CI/workflow files and package manager config (for command/tooling mismatches)
```

### Maintenance Mindset

```text { .api }
AGENTS.md is temporary guidance, not permanent configuration.

When recurring issues appear:
1. Prefer fixing the root cause in code/tooling (lint rule, test, script, structure)
2. Keep only the minimum instruction needed until the root cause is solved
3. Prune stale instructions aggressively

For large repos, recommend hierarchical AGENTS.md files near relevant modules
instead of one monolithic root file.
```

## Tile Manifest

```json { .api }
// skills/init/tile.json
{
  "name": "mcollina/init",
  "version": "0.1.0",
  "private": false,
  "summary": "Creates, updates, or optimizes an AGENTS.md file for a repository with minimal, high-signal instructions.",
  "skills": {
    "init": {
      "path": "SKILL.md"
    }
  }
}
```
