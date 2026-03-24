# IXS Agent Skills

Public, reusable agent skills for IXS workflows.

This repository is a tool-agnostic skill hub. Each published skill lives under [`skills/`](./skills) as a self-contained folder with a required `SKILL.md` entrypoint and optional supporting material.

## What This Repo Is

- A public catalog of IXS-specific agent skills.
- A lightweight contract for sharing skills across Codex/OpenAI-compatible agents, OpenClaw, and similar runtimes.
- A place to keep operational instructions concise in `SKILL.md` while moving heavier references into per-skill support folders.

## Available Skills

| Skill | Purpose | Owner | Status | Primary Runtime | IXS Surface Area |
| --- | --- | --- | --- | --- | --- |
| `inspect-vault` | Inspect a public IXS vault and wallet position before an investment decision. | IXS Protocol | active | Codex/OpenAI-compatible | public vault API |
| `quote-vault-deposit` | Preview deposit shares and current deposit limits without executing a transaction. | IXS Protocol | active | Codex/OpenAI-compatible | public ERC-4626 vault |
| `quote-vault-redeem` | Preview redeem asset output, fees, and redeem limits without executing a transaction. | IXS Protocol | active | Codex/OpenAI-compatible | public ERC-4626 vault |
| `check-vault-allowance` | Inspect whether the wallet has enough ERC-20 allowance for a target deposit. | IXS Protocol | active | Codex/OpenAI-compatible | public ERC-20 and ERC-4626 vault |
| `approve-vault-spender` | Plan or execute the ERC-20 approval step for an ERC-4626 vault deposit. | IXS Protocol | active | Codex/OpenAI-compatible | public ERC-20 and ERC-4626 vault |
| `deposit-into-vault` | Plan or execute a vault deposit using quote and intent endpoints. | IXS Protocol | active | Codex/OpenAI-compatible | public vault API |
| `redeem-from-vault` | Plan or execute a vault redeem using quote and intent endpoints. | IXS Protocol | active | Codex/OpenAI-compatible | public vault API |
| `review-vault-wallet-history` | Summarize a wallet's recent vault deposit and redeem activity. | IXS Protocol | active | Codex/OpenAI-compatible | public vault history |
| `compare-vault-entry-vs-exit` | Compare deposit and redeem conditions for a wallet before acting. | IXS Protocol | active | Codex/OpenAI-compatible | public ERC-4626 vault |

## Repo Contract

- Consumers should treat `skills/<skill-slug>/SKILL.md` as the skill entrypoint.
- Optional subfolders such as `references/`, `scripts/`, `assets/`, and `examples/` are supportive only and must not be required for discovery.
- Each `SKILL.md` starts with YAML metadata so simple tooling can index skills without parsing the full document.

## Supported Consumers

- Codex/OpenAI-compatible local skill folders
- OpenClaw-style local or submodule installs
- Generic local-copy or symlink workflows for other agent runtimes

Install docs:

- [`docs/installation/codex.md`](./docs/installation/codex.md)
- [`docs/installation/openclaw.md`](./docs/installation/openclaw.md)
- [`docs/installation/generic-local.md`](./docs/installation/generic-local.md)

## Quick Start

1. Clone this repository.
2. Choose one or more skills from [`skills/`](./skills).
3. Install them into your target runtime using the docs above.
4. Provide the required environment variables for the chosen skill.
5. Verify the runtime discovers the skill and that trigger prompts behave as expected.

## Add A Skill

Start with:

- [`docs/adding-a-skill.md`](./docs/adding-a-skill.md)
- [`docs/skill-authoring-guidelines.md`](./docs/skill-authoring-guidelines.md)
- [`docs/governance.md`](./docs/governance.md)

## Examples

- [`examples/local-install.md`](./examples/local-install.md)
- [`examples/skill-index-example.md`](./examples/skill-index-example.md)

## Validation

GitHub Actions validates the skill contract on pull requests:

- every folder in `skills/` contains `SKILL.md`
- required metadata fields exist
- slugs follow lowercase hyphen-case
- declared skill names are unique

## Versioning

This repo keeps versioning simple:

- each skill declares its own `version` in metadata
- repo-wide release automation is intentionally deferred
- breaking skill changes should update the skill `version` and relevant docs in the same PR
