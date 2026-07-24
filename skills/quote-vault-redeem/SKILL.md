---
name: quote-vault-redeem
description: Preview the expected asset output, fees, and current redeem limit for a public IXS ERC-4626 vault without executing a transaction.
owner: IXS Protocol
status: active
version: 1.1.0
tags:
  - vaults
  - erc4626
  - quote
  - mcp
tools:
  - codex
  - openclaw
repo_scope:
  - public-erc4626-vault
  - public-vault-mcp
last_reviewed: 2026-07-23
---

# Quote Vault Redeem

## When To Use

Use this skill when the user wants to preview redeem output, fees, and limits before redeeming from a public IXS ERC-4626 vault.

## Inputs

- `IXS_MCP_URL`
- `IXS_VAULT_ADDRESS`
- `IXS_VAULT_CHAIN_ID`
- target share amount
- wallet from an approved signer; use `AGENT_PRIVATE_KEY` only for isolated test or compatibility setups

## Trigger Examples

- "Quote redeeming 25 shares from the configured vault."
- "How much asset would I receive and what fee would be charged if I redeem now?"

## Procedure

1. Call the MCP tool `vault_get` (or otherwise determine settlement kind) first — the read surface differs between `sync`/`queued` (`ManagedVault`-kind) and `async-erc7540` (`ERC7540OperatedVault`-kind) vaults.
2. Read `paused()`, `totalAssets()`, and `availableAssets()` from the vault.
3. Read `balanceOf(owner)` and `maxRedeem(owner)`.
4. Read `previewRedeem(shares)`. On `ManagedVault`-kind vaults, also read `previewRedeemFee(shares)` for a separate fee figure. On `ERC7540OperatedVault`-kind vaults, do not call `previewRedeemFee` — it does not exist on that contract; the fee is already netted into `previewRedeem`'s return value.
5. Summarize:
   - paused state
   - requested shares
   - expected net assets out
   - expected redeem fee (`ManagedVault`-kind only — for `ERC7540OperatedVault`-kind vaults, state that the figure is already net of fee instead of reporting a separate fee amount)
   - current max redeem
   - wallet share balance
   - total and available assets
6. If the requested share amount exceeds either wallet balance or `maxRedeem`, say so clearly — except on `async-erc7540` vaults, where `maxRedeem` always returns `0` by design (the synchronous path is disabled; redeems only go through `requestRedeem`). Do not report that as "redeem blocked" — say instead that this vault only accepts redeems via the async request/claim flow (`redeem-from-vault`).

## Guardrails

- Do not present the quote as a guaranteed execution result.
- Treat low available assets as execution risk even when preview calls succeed.
- On `async-erc7540` vaults, `previewRedeem` is an explicitly non-binding estimate at the current indicative price — the real conversion is priced when the request is finalized, which can differ from this quote. Say so; do not present it with the same confidence as a sync-vault quote.
- Never call `previewRedeemFee` on an `async-erc7540` vault — it will revert.
- Stop if the vault read calls fail.

## References

- `references/read-surface.md`
