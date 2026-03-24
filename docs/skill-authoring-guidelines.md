# Skill Authoring Guidelines

These guidelines keep skills readable, portable, and easy to validate.

## Folder Naming

- use lowercase hyphen-case for the skill folder name
- keep the folder slug stable after publication unless a rename is unavoidable

## Required Metadata

Each `SKILL.md` must begin with YAML frontmatter:

```yaml
---
name: example-skill
description: One-sentence summary of what the skill does and when to use it.
owner: IXS Team
status: active
version: 1.0.0
tags:
  - example
tools:
  - codex
repo_scope:
  - ixs-ui
last_reviewed: 2026-03-24
---
```

Required fields:

- `name`
- `description`
- `owner`
- `status`
- `version`

Recommended fields:

- `tags`
- `tools`
- `repo_scope`
- `last_reviewed`

Allowed `status` values:

- `draft`
- `active`
- `deprecated`

## Body Structure

Keep the body short and operational. Use this order when possible:

1. Title
2. When to use
3. Trigger examples
4. Workflow
5. Constraints
6. References

## Writing Rules

- prefer imperative instructions
- keep the skill entrypoint concise
- include at least 2 trigger examples
- move long background material into `references/`
- avoid tool-specific assumptions unless the skill is intentionally runtime-specific
- if the skill depends on another repo, name that repo explicitly

## Trigger Quality

Good trigger examples are:

- concrete
- action-oriented
- close to the real user language that should activate the skill

Weak trigger examples are:

- vague
- generic to the point that they match unrelated tasks

## Support Material

Use support folders only when they add value:

- `references/` for deeper docs or domain notes
- `scripts/` for reusable commands or helper tooling
- `assets/` for diagrams or templates
- `examples/` for prompt or install samples
