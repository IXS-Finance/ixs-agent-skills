# Repo Overview

`ixs-agent-skills` is a public repository of reusable agent skills for IXS engineering, product, and operational workflows.

## Design Goals

- Keep each skill self-contained and easy to install.
- Support multiple agent runtimes without centering the repo around one tool.
- Make the contract simple enough for humans and lightweight validators.
- Keep public documentation clear about any dependency on private or adjacent IXS repos.

## Directory Layout

- `skills/`
  - source of truth for published skills
  - one folder per skill
- `docs/`
  - contribution, governance, and installation guidance
- `examples/`
  - small reference snippets for consumers and contributors
- `.github/workflows/`
  - CI validation for the repo contract

## Skill Folder Contract

Each skill lives in `skills/<skill-slug>/`.

Required:

- `SKILL.md`

Optional:

- `references/`
- `scripts/`
- `assets/`
- `examples/`

Consumers should not depend on optional folders existing.

## Metadata Contract

Each `SKILL.md` begins with YAML frontmatter containing:

- `name`
- `description`
- `owner`
- `status`
- `version`

Recommended metadata:

- `tags`
- `tools`
- `repo_scope`
- `last_reviewed`

## Initial Skill Set

The initial repository is centered on public vault workflows:

- vault inspection
- deposit quoting
- redeem quoting
- allowance preflight and approval
- deposit planning and execution
- redeem planning and execution
- wallet history review
- entry and exit comparison
