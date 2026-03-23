# Documentation Skill

The `documentation` skill creates, structures, and reviews technical documentation following the Diátaxis framework. It organizes content into four distinct types — tutorials, how-to guides, reference material, and explanations — each serving different user needs and contexts.

## Skill Reference

```text { .api }
Skill name:  documentation
Tile name:   mcollina/documentation
Version:     0.1.0
Path:        skills/documentation/SKILL.md
Tile:        skills/documentation/tile.json
Tags:        documentation, technical-writing, tutorials, guides, reference, diataxis
```

## When to Use

- Creating user guides or API documentation
- Writing tutorial content for new users
- Restructuring existing documentation to better serve different audiences
- Choosing between Diátaxis documentation types
- Improving existing documentation structure

**Trigger terms**: documentation structure, Diátaxis, tutorials vs how-to guides, organize docs, user guide, reference docs, technical writing

## Capabilities

### Diátaxis Framework Decision

Identifies the correct documentation type based on user signals:

```text { .api }
| User Signal                                        | Documentation Type |
|----------------------------------------------------|-------------------|
| "I'm new to X and want to learn it" / "walk me through" | Tutorial      |
| "How do I...?" / "I need to accomplish X"          | How-to guide      |
| "What are the parameters/options/syntax for X?"    | Reference         |
| "Why does X work this way?" / "Help me understand X"| Explanation       |
```

**Quick decision tree**:
- User is **learning by doing** for the first time → Tutorial
- User needs to **solve a specific problem** they already understand → How-to guide
- User needs **technical facts** to look up → Reference
- User wants **conceptual background** → Explanation

### Tutorial Authoring

Learning-oriented documentation for first-time learners.

```text { .api }
Title pattern:  Start with a verb — "Build your first X", "Create a Y from scratch"
Structure:      Goal → Prerequisites → Numbered steps → Immediate verifiable result → Final outcome
Constraint:     Every step must produce a visible, testable result
Validation:     A beginner must be able to complete without external help
```

### How-to Guide Authoring

Task-oriented documentation for users who know what they need to accomplish.

```text { .api }
Title pattern:  "How to X", "X with Y"
Structure:      Problem statement → Prerequisites → Numbered steps → Expected result
Constraint:     Must assume the reader already understands the domain
Focus:          Task completion, not explanation
```

### Reference Documentation

Information-oriented documentation for users who need technical facts.

```text { .api }
Structure:      Consistent schema per item (name, type, description, example)
Tone:           Neutral, factual, no procedural steps
Organization:   Alphabetical or by category
Constraint:     No tutorials or how-to steps mixed in
```

### Explanation / Conceptual Documentation

Understanding-oriented documentation that provides context and background.

```text { .api }
Title pattern:  "Understanding X", "Why X works this way", "X explained"
Structure:      Context → Concepts → Connections → Implications
Focus:          Insight and understanding, not tasks or facts
```

### Maintaining Separation and Integration

```text { .api }
Rules for maintaining doc quality across a documentation set:
- Keep each document a single type — don't mix tutorial steps with reference
  tables or conceptual digressions
- Cross-link between types: a tutorial can link to the relevant reference page;
  a how-to guide can link to an explanation for background
- Use consistent headings and terminology across all types so users can
  navigate the full documentation system
```

### Validate Before Delivering

```text { .api }
| Type        | Validation check                                                     |
|-------------|----------------------------------------------------------------------|
| Tutorial    | Can a beginner complete it end-to-end without external help?         |
| How-to guide| Does it solve the stated problem for an experienced user?            |
| Reference   | Can the user find a specific fact in under 30 seconds?               |
| Explanation | Does the user understand the *why*, not just the *what*?             |
```

## Tile Manifest

```json { .api }
// skills/documentation/tile.json
{
  "name": "mcollina/documentation",
  "version": "0.1.0",
  "private": false,
  "summary": "Creates, structures, and reviews technical documentation following the Diátaxis framework (tutorials, how-to guides, reference, and explanation pages).",
  "skills": {
    "documentation": {
      "path": "SKILL.md"
    }
  }
}
```
