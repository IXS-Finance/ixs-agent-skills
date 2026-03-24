---
name: review-vault-wallet-history
description: Summarize a wallet's recent deposit and redeem activity for a public IXS vault using available history sources.
owner: IXS Protocol
status: active
version: 1.0.0
tags:
  - vaults
  - history
  - wallet
tools:
  - codex
  - openclaw
repo_scope:
  - public-vault-history
last_reviewed: 2026-03-24
---

# Review Vault Wallet History

## When To Use

Use this skill when the user wants a summary of recent deposit and redeem activity for a wallet on a public IXS vault.

## Inputs

- `IXS_VAULT_ADDRESS`
- `IXS_VAULT_CHAIN_ID`
- wallet address
- history source available to the runtime

## Trigger Examples

- "Show my recent vault deposit and redeem activity."
- "Review this wallet's vault history and summarize the latest actions."

## Procedure

1. Load the wallet's available vault transaction history from the runtime's supported source.
2. Normalize rows into deposit and redeem actions with status and timestamp.
3. Sort newest first.
4. Summarize:
   - most recent deposit
   - most recent redeem
   - pending items
   - rejected items
   - total recent activity count in the returned data

## Guardrails

- Do not claim the history is complete unless the source is known to be complete.
- Preserve the source status values rather than inferring settlement.
- Stop if no readable history source is available.

## References

- `references/history-sources.md`
