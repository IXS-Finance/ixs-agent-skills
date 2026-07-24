---
name: review-vault-wallet-history
description: Summarize a wallet's recent deposit and redeem activity for a public IXS vault using available history sources.
owner: IXS Protocol
status: active
version: 1.1.0
tags:
  - vaults
  - history
  - wallet
  - mcp
tools:
  - codex
  - openclaw
repo_scope:
  - public-vault-history
  - public-vault-mcp
last_reviewed: 2026-07-23
---

# Review Vault Wallet History

## When To Use

Use this skill when the user wants a summary of recent deposit and redeem activity for a wallet on a public IXS vault.

## Inputs

- `IXS_MCP_URL`
- `IXS_VAULT_ADDRESS`
- `IXS_VAULT_CHAIN_ID`
- wallet address
- history source available to the runtime

## Trigger Examples

- "Show my recent vault deposit and redeem activity."
- "Review this wallet's vault history and summarize the latest actions."

## Procedure

1. Call the MCP tool `vault_request_status` with the wallet's address (and `vaultId` if scoping to one vault) — this is the canonical source of deposit/redeem request history for async vaults, and reports each request's status and claimable state.
2. If the runtime has another supported history source (e.g. persisted local wallet history), load it too and merge with the `vault_request_status` result rather than replacing it.
3. Normalize rows into deposit and redeem actions with status and timestamp.
4. Sort newest first.
5. Summarize:
   - most recent deposit
   - most recent redeem
   - pending items
   - claimable items (call out that `deposit-into-vault`/`redeem-from-vault`'s claim step still needs to run for these)
   - rejected items
   - total recent activity count in the returned data

## Guardrails

- Do not claim the history is complete unless the source is known to be complete.
- Preserve the source status values rather than inferring settlement.
- Stop if no readable history source is available.
- A "claimable" request is not yet settled — do not describe its assets or shares as already in the wallet.

## References

- `references/history-sources.md`
