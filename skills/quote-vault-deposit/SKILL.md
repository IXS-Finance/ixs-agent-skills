---
name: quote-vault-deposit
description: Preview the expected share output and current deposit limits for a public IXS ERC-4626 vault without executing a transaction.
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

# Quote Vault Deposit

## When To Use

Use this skill when the user wants to preview deposit outcomes before approving or depositing into a public IXS ERC-4626 vault.

## Inputs

- `IXS_MCP_URL`
- `IXS_VAULT_ADDRESS`
- `IXS_VAULT_CHAIN_ID`
- target deposit asset amount
- optional wallet from an approved signer; use `AGENT_PRIVATE_KEY` only for isolated test or compatibility setups when wallet-specific limits are needed

## Trigger Examples

- "Quote a 500 USDC deposit into the configured ERC-4626 vault."
- "How many shares would I get if I deposit 1,000 into this vault right now?"

## Procedure

1. Call the MCP tool `vault_get` (or otherwise determine settlement kind) before reading anything else — `sync` (`ManagedVault`-kind) and `async-erc7540` (`ERC7540OperatedVault`-kind) vaults must be summarized differently in step 4.
2. Read `asset()`, `paused()`, `totalAssets()`, and `availableAssets()` from the vault.
3. Read `maxDeposit(receiver)` for the target wallet if available.
4. Read `previewDeposit(assets)` for the requested asset amount.
5. Summarize:
   - paused state
   - requested asset amount
   - expected shares out
   - current max deposit
   - total and available assets
6. If the requested amount exceeds the current limit, say so clearly — except on `async-erc7540` vaults, where `maxDeposit` always returns `0` by design (the synchronous path is disabled; deposits only go through `requestDeposit`). Do not report that as "deposit blocked" — say instead that this vault only accepts deposits via the async request/claim flow (`deposit-into-vault`).

## Guardrails

- Do not present a quote as a guaranteed execution result.
- Do not treat `previewDeposit` as proof that the wallet has enough balance or allowance.
- On `async-erc7540` vaults, `previewDeposit` is an explicitly non-binding estimate at the current indicative price — the real conversion is priced when the request is finalized, which can differ from this quote. Say so; do not present it with the same confidence as a sync-vault quote.
- Stop if the vault read calls fail.

## References

- `references/read-surface.md`
