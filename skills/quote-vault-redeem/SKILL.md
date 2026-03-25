---
name: quote-vault-redeem
description: Preview the expected asset output, fees, and current redeem limit for a public IXS ERC-4626 vault without executing a transaction.
owner: IXS Protocol
status: active
version: 1.0.1
tags:
  - vaults
  - erc4626
  - quote
tools:
  - codex
  - openclaw
repo_scope:
  - public-erc4626-vault
last_reviewed: 2026-03-25
---

# Quote Vault Redeem

## When To Use

Use this skill when the user wants to preview redeem output, fees, and limits before redeeming from a public IXS ERC-4626 vault.

## Inputs

- `IXS_VAULT_ADDRESS`
- `IXS_VAULT_CHAIN_ID`
- target share amount
- wallet from an approved signer; use `AGENT_PRIVATE_KEY` only for isolated test or compatibility setups

## Trigger Examples

- "Quote redeeming 25 shares from the configured vault."
- "How much asset would I receive and what fee would be charged if I redeem now?"

## Procedure

1. Read `paused()`, `totalAssets()`, and `availableAssets()` from the vault.
2. Read `balanceOf(owner)` and `maxRedeem(owner)`.
3. Read `previewRedeem(shares)` and `previewRedeemFee(shares)`.
4. Summarize:
   - paused state
   - requested shares
   - expected gross assets out
   - expected redeem fee
   - current max redeem
   - wallet share balance
   - total and available assets
5. If the requested share amount exceeds either wallet balance or `maxRedeem`, say so clearly.

## Guardrails

- Do not present the quote as a guaranteed execution result.
- Treat low available assets as execution risk even when preview calls succeed.
- Stop if the vault read calls fail.

## References

- `references/read-surface.md`
