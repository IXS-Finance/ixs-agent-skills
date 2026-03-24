# IXS Agent Skills

![License](https://img.shields.io/badge/license-MIT-green)
![Skills](https://img.shields.io/badge/skills-9-blue)
![Network](https://img.shields.io/badge/network-Base%20Sepolia-0052FF)

Agent skills for working with the IXS ERC-4626 vault flow.

## What this is

This repo is a collection of reusable agent skills that teach an agent how to inspect, quote, compare, approve, deposit into, redeem from, and review history for IXS vaults.

Each skill lives in [`skills/`](./skills) as its own folder. The entrypoint is `SKILL.md`, with optional support material in folders like `references/`, `scripts/`, `assets/`, or `examples/`.

The repo is designed to work with Codex/OpenAI-compatible local skill folders, OpenClaw-style installs, and similar runtimes that load skills from the filesystem.

## Why this exists

Vault interactions look simple on paper, but safe agent execution still needs structure:

- agents need deterministic sequences for read-only checks, approvals, and execution
- ERC-20 allowance and balance checks are easy to skip or muddle
- quote and intent data should be treated as planning inputs, not invented by the agent
- integrators need reusable vault workflows instead of rebuilding the same prompt logic per runtime

## Safety

- Read-only skills do not move funds.
- Transaction-capable skills should only execute when the caller explicitly chooses execute mode.
- Skills in this repo are workflow guardrails, not financial advice.

## Coverage

Current coverage is grouped by intent.

### Research

- `inspect-vault`
- `review-vault-wallet-history`
- `compare-vault-entry-vs-exit`

### Preflight

- `quote-vault-deposit`
- `quote-vault-redeem`
- `check-vault-allowance`

### Actions

- `approve-vault-spender`
- `deposit-into-vault`
- `redeem-from-vault`

## Skills

| Skill | Purpose |
| --- | --- |
| `inspect-vault` | Inspect a public IXS vault and wallet position before an investment decision. |
| `quote-vault-deposit` | Preview deposit shares and current deposit limits without executing a transaction. |
| `quote-vault-redeem` | Preview redeem asset output, fees, and redeem limits without executing a transaction. |
| `check-vault-allowance` | Inspect whether the wallet has enough ERC-20 allowance for a target deposit. |
| `approve-vault-spender` | Plan or execute the ERC-20 approval step for an ERC-4626 vault deposit. |
| `deposit-into-vault` | Plan or execute a vault deposit using the public API quote and intent flow. |
| `redeem-from-vault` | Plan or execute a vault redeem using the public API quote and intent flow. |
| `review-vault-wallet-history` | Summarize a wallet's recent vault deposit and redeem activity. |
| `compare-vault-entry-vs-exit` | Compare deposit and redeem conditions for a wallet before acting. |

## Quickstart

1. Clone this repository.
2. Copy or symlink one or more skill folders from [`skills/`](./skills) into your runtime's skills directory.
3. Fill in [`.env.example`](./.env.example) values for your environment.
4. Reload the runtime.
5. Start with a read-only flow:
   - `inspect-vault`
   - `quote-vault-deposit`
   - `check-vault-allowance`

## Using these skills

To use this hub:

1. Clone this repository.
2. Choose one or more skill folders from [`skills/`](./skills).
3. Copy or symlink those folders into your agent runtime's skills directory, or point the runtime at this repo's `skills/` directory directly.
4. Set the environment variables required by the chosen skill.
5. Start a new session or reload the runtime so it picks up the skills.

For API-driven skills, set `IXS_API_BASE_URL` and `IXS_VAULT_ID`.
For direct contract-read and execution skills, set the vault address, asset address, chain id, and Base Sepolia RPC URL.

Install details:

- [`docs/installation/codex.md`](./docs/installation/codex.md)
- [`docs/installation/openclaw.md`](./docs/installation/openclaw.md)
- [`docs/installation/generic-local.md`](./docs/installation/generic-local.md)
- [`docs/integrator-notes.md`](./docs/integrator-notes.md)

Security guidance:

- [`docs/security-guidelines.md`](./docs/security-guidelines.md)

## End-to-end example

For a minimal ERC-4626 vault flow, see:

- [`examples/erc4626-happy-path.md`](./examples/erc4626-happy-path.md)

## Skill format

Each skill folder includes:

- `SKILL.md` as the required entrypoint
- YAML metadata at the top of `SKILL.md`
- optional support files in `references/`, `scripts/`, `assets/`, or `examples/`

Consumers should treat `SKILL.md` as the only required discovery surface.

## Add a skill

Contributor docs:

- [`docs/adding-a-skill.md`](./docs/adding-a-skill.md)
- [`docs/skill-authoring-guidelines.md`](./docs/skill-authoring-guidelines.md)
- [`docs/governance.md`](./docs/governance.md)
- [`docs/security-guidelines.md`](./docs/security-guidelines.md)

Examples:

- [`examples/local-install.md`](./examples/local-install.md)
- [`examples/erc4626-happy-path.md`](./examples/erc4626-happy-path.md)
- [`examples/skill-index-example.md`](./examples/skill-index-example.md)

## Validation

GitHub Actions validates the repo structure on push and pull request:

- every folder in `skills/` contains `SKILL.md`
- required metadata fields exist
- slugs use lowercase hyphen-case
- declared skill names are unique

## Versioning

Each skill declares its own `version` in metadata. This repo does not currently use a separate release system for the hub itself.
