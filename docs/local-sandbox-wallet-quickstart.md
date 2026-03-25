# Local Sandbox Wallet Quickstart

Use this guide when you need to test the skills locally without weakening the production security policy.

This is for local development only.

## Goals

- make local testing easy
- keep `AGENT_PRIVATE_KEY` support available for disposable test wallets
- avoid normalizing unsafe patterns for shared or production runtimes

## Rules

- use a disposable burner wallet only
- use testnet only unless you have a very specific reason not to
- keep the wallet balance low and treat it as already compromised
- never paste the private key into chat, prompts, tickets, or screenshots
- never reuse the same burner key across teammates or environments
- rotate the key immediately if it appears in shell history, logs, or transcripts

## Recommended Local Setup

1. Create a fresh burner wallet for local testing.
2. Fund it with only the minimum amount needed for the test flow.
3. Store the key in a local `.env` that is gitignored and never copied into `.env.example`.
4. Point the runtime at testnet RPC and testnet contracts only.
5. Run read-only flows first.
6. Use execute mode only after verifying chain, contract, spender, and amount.
7. Destroy or rotate the burner wallet after the test cycle if there is any chance the key leaked.

## Minimum Guardrails Even In Local Dev

- keep `AGENT_PRIVATE_KEY` scoped to one local process or shell session
- do not run a signer-enabled local session against a shared agent backend
- do not use a wallet that also holds long-lived assets
- do not mix local sandbox keys with CI, staging, or production secrets

## What This Guide Does Not Permit

This guide does not make raw private keys acceptable for:

- public agents
- shared team assistants
- treasury or admin operations
- multi-tenant relayers
- unattended production automation without separate policy controls

## Related Docs

- [`docs/wallet-private-key-security.md`](./wallet-private-key-security.md)
- [`docs/security-guidelines.md`](./security-guidelines.md)
