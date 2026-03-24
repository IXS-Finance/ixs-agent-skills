# IXS Agent Skills

Agent skills for working with IXS vault workflows, starting with the public ERC-4626 vault flow.

## What this is

This repo is a collection of reusable agent skills that teach an agent how to inspect, quote, compare, approve, deposit into, redeem from, and review history for IXS vaults.

Each skill lives in [`skills/`](./skills) as its own folder. The entrypoint is `SKILL.md`, with optional support material in folders like `references/`, `scripts/`, `assets/`, or `examples/`.

The repo is designed to work with Codex/OpenAI-compatible local skill folders, OpenClaw-style installs, and similar runtimes that load skills from the filesystem.

## Coverage

Current coverage includes:

- vault inspection and wallet position checks
- deposit quoting
- redeem quoting
- allowance checks
- approval planning and execution
- deposit planning and execution
- redeem planning and execution
- wallet history review
- entry-versus-exit comparison

## Skills

| Skill | Purpose |
| --- | --- |
| `inspect-vault` | Inspect a public IXS vault and wallet position before an investment decision. |
| `quote-vault-deposit` | Preview deposit shares and current deposit limits without executing a transaction. |
| `quote-vault-redeem` | Preview redeem asset output, fees, and redeem limits without executing a transaction. |
| `check-vault-allowance` | Inspect whether the wallet has enough ERC-20 allowance for a target deposit. |
| `approve-vault-spender` | Plan or execute the ERC-20 approval step for an ERC-4626 vault deposit. |
| `deposit-into-vault` | Plan or execute a vault deposit using the public API flow. |
| `redeem-from-vault` | Plan or execute a vault redeem using the public API flow. |
| `review-vault-wallet-history` | Summarize a wallet's recent vault deposit and redeem activity. |
| `compare-vault-entry-vs-exit` | Compare deposit and redeem conditions for a wallet before acting. |

## Using these skills

To use this hub:

1. Clone this repository.
2. Choose one or more skill folders from [`skills/`](./skills).
3. Copy or symlink those folders into your agent runtime's skills directory, or point the runtime at this repo's `skills/` directory directly.
4. Set the environment variables required by the chosen skill.
5. Start a new session or reload the runtime so it picks up the skills.

Install details:

- [`docs/installation/codex.md`](./docs/installation/codex.md)
- [`docs/installation/openclaw.md`](./docs/installation/openclaw.md)
- [`docs/installation/generic-local.md`](./docs/installation/generic-local.md)

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

Examples:

- [`examples/local-install.md`](./examples/local-install.md)
- [`examples/skill-index-example.md`](./examples/skill-index-example.md)

## Validation

GitHub Actions validates the repo structure on push and pull request:

- every folder in `skills/` contains `SKILL.md`
- required metadata fields exist
- slugs use lowercase hyphen-case
- declared skill names are unique

## Versioning

Each skill declares its own `version` in metadata. This repo does not currently use a separate release system for the hub itself.
