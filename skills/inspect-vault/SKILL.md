---
name: inspect-vault
description: Inspect a public IXS tokenized vault and the current wallet position before making an investment decision.
owner: IXS Protocol
status: active
version: 2.0.0
tags:
  - vaults
  - defi
  - inspection
  - mcp
tools:
  - codex
  - openclaw
repo_scope:
  - public-vault-api
  - public-vault-mcp
last_reviewed: 2026-07-23
---

# Inspect Vault

## When To Use

Use this skill to inspect a public IXS tokenized vault and the current wallet position before making any investment decision.

## Inputs

- `IXS_MCP_URL`
- `IXS_API_BASE_URL`
- `IXS_VAULT_ID`
- wallet from an approved signer; use `AGENT_PRIVATE_KEY` only for isolated test or compatibility setups

## Trigger Examples

- "Inspect the configured IXS vault and summarize my current wallet position."
- "Before I deposit, check whether this vault looks liquid and show my balances."

## Procedure

1. Call the MCP tool `vault_get` with `vaultId`. This returns vault metadata and `settlement` (`sync` for `ManagedVault`-kind vaults, `async-erc7540` for `ERC7540OperatedVault`-kind vaults) — read `settlement` before recommending any deposit or redeem skill, since it changes what "done" means for those flows.
2. Read `paused()`, `totalAssets()`, and `availableAssets()` directly from the vault contract at `vault.contractAddress` (from step 1) on `vault.chainId`.
3. Call `GET /vaults/{vaultId}/positions/{walletAddress}` for the wallet's asset balance, share balance, allowance to the vault, max withdraw, and max redeem.
4. If `settlement` is `async-erc7540`, call the MCP tool `vault_request_status` for the wallet to check for pending or claimable deposit/redeem requests.
5. Summarize:
   - settlement kind (sync or async-erc7540)
   - paused state
   - total assets
   - available assets
   - liquidity shortfall or surplus
   - wallet asset balance
   - wallet share balance
   - allowance to vault
   - max withdraw
   - max redeem
   - any pending or claimable requests (async vaults only)

## Guardrails

- Do not assume the vault is liquid unless the read data says so.
- Do not invent balances, allowances, or a settlement kind — always read it from `vault_get`.
- Stop if a tool call or contract read returns an error.
- `GET /vaults/{vaultId}/metrics` does not exist — do not call it. Vault-level totals come from direct on-chain reads (step 2), not from the REST API.

## References

- `references/api-notes.md`
