# Skill Optimizer Skill

The `skill-optimizer` skill optimizes AI skills for activation, clarity, and cross-model reliability. It provides workflows for benchmarking, activation design, context budget management, regression triage, and release gates.

## Skill Reference

```text { .api }
Skill name:  skill-optimizer
Path:        skills/skill-optimizer/SKILL.md
Note:        No tile.json (not published to registry separately)
Tags:        skills, optimization, benchmarking, activation, regressions, prompt-engineering
```

## When to Use

- Improving whether a skill is actually applied by models
- Diagnosing why some criteria fail across all models
- Preventing a skill from making outputs worse (regressions)
- Refactoring skill text for stronger retrieval under context pressure
- Building repeatable benchmark loops and release gates

**Trigger terms**: skill optimization, activation gap, benchmark skill, with/without skill delta, regression, context budget, prompt salience

## Rule Sub-Documents

```text { .api }
skills/skill-optimizer/rules/
  activation-design.md    - Improve skill retrieval and instruction uptake
  benchmark-loop.md       - End-to-end benchmark loop and scoring procedures
  context-budget.md       - Reduce token cost without losing behavior
  regression-triage.md    - Diagnose and fix skill-on regressions
  release-gates.md        - Go/no-go criteria before shipping skill updates
```

## Key Capabilities

### Optimization Loop (Default Workflow)

```text { .api }
1. Measure baseline and skill-on behavior
   - Per model, per scenario, per criterion
2. Find failure pattern:
   - Universal failure (0% with skill) → activation problem
   - Model-specific weakness → rephrase for that model's style
   - Regression (negative delta) → identify and remove conflicting rules
3. Edit for salience:
   - Add explicit trigger terms
   - Add concrete integrated examples
   - Tighten checklists and decision rules
4. Re-run evaluations and compare deltas
5. Ship with guardrails: documented gate + run history + follow-up issues
```

### Activation Design

```text { .api }
Techniques for improving skill retrieval and uptake:
- Put the most important constraints in a top-level checklist (not buried in prose)
- Use explicit trigger phrases in the description field
- Use concrete examples in addition to abstract rules
- Write instructions as imperative commands, not suggestions
- Avoid passive voice and hedging language
- Order instructions by priority (most impactful first)
```

### Benchmark Loop

```text { .api }
Benchmark structure:
- Scenario: specific user prompt that should activate the skill
- Criterion: observable output property (present/absent/correct)
- Score: binary (pass/fail) per criterion per scenario
- Delta: skill-on score minus skill-off score per criterion

Minimum viable benchmark:
- 3-5 scenarios covering different trigger paths
- 2-3 criteria per scenario
- Compare across at least 2 models
```

### Context Budget Management

```text { .api }
Optimization goals:
- Every instruction should earn its token cost
- Remove redundant/overlapping rules
- Merge related rules into single actionable items
- Move detailed examples to rules/ sub-documents (reference from SKILL.md)
- Use tables and code blocks rather than prose for structured info

Warning signs of context bloat:
- SKILL.md > 300 lines
- Multiple rules saying the same thing differently
- Examples that show obvious behavior (not edge cases)
```

### Regression Triage

```text { .api }
Regression = skill-on produces worse output than skill-off

Triage process:
1. Identify the exact criterion with negative delta
2. Find the instruction causing the bad behavior
3. Determine: wrong instruction or correct instruction wrongly applied
4. Options:
   a. Remove or narrow the offending instruction
   b. Add a qualifier or guard condition
   c. Reorder (later instructions override earlier ones for some models)
5. Re-benchmark to confirm regression is resolved without losing gains
```

### Release Gates

```text { .api }
Minimum bar before shipping skill updates:
- No regressions (negative delta) on any existing benchmark criterion
- At least one criterion improved or maintained on primary scenarios
- Tested on minimum 2 models
- Benchmark run saved and linked in PR/commit

Hard blocks (do not ship if any apply):
- Skill-on makes output worse on >20% of criteria
- Any new regression introduced in previously-green criteria
- Context size increased without measurable benefit
```

## Practical Heuristics

```text { .api }
- Prefer few high-signal rules over many soft recommendations
- Put fragile, high-value behaviors in top-level checklists
- Include at least one integrated example per common scenario
- Add explicit wording for what must NOT be omitted
- Track gains/losses with with-skill vs without-skill comparisons
- Test with adversarial prompts (prompts that should NOT trigger the skill)
```
