---
name: quote-vault-deposit
description: Preview the expected share output and current deposit limits for a public IXS ERC-4626 vault without executing a transaction.
owner: IXS Protocol
status: active
version: 1.0.0
tags:
  - vaults
  - erc4626
  - quote
tools:
  - codex
  - openclaw
repo_scope:
  - public-erc4626-vault
last_reviewed: 2026-03-24
---

# Quote Vault Deposit

## When To Use

Use this skill when the user wants to preview deposit outcomes before approving or depositing into a public IXS ERC-4626 vault.

## Inputs

- `IXS_VAULT_ADDRESS`
- `IXS_VAULT_CHAIN_ID`
- target deposit asset amount
- optional wallet from `AGENT_PRIVATE_KEY` if wallet-specific limits are needed

## Trigger Examples

- "Quote a 500 USDC deposit into the configured ERC-4626 vault."
- "How many shares would I get if I deposit 1,000 into this vault right now?"

## Procedure

1. Read `asset()`, `paused()`, `totalAssets()`, and `availableAssets()` from the vault.
2. Read `maxDeposit(receiver)` for the target wallet if available.
3. Read `previewDeposit(assets)` for the requested asset amount.
4. Summarize:
   - paused state
   - requested asset amount
   - expected shares out
   - current max deposit
   - total and available assets
5. If the requested amount exceeds the current limit, say so clearly.

## Guardrails

- Do not present a quote as a guaranteed execution result.
- Do not treat `previewDeposit` as proof that the wallet has enough balance or allowance.
- Stop if the vault read calls fail.

## References

- `references/read-surface.md`
