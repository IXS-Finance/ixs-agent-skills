---
name: inspect-vault
description: Inspect a public IXS tokenized vault and the current wallet position before making an investment decision.
owner: IXS Protocol
status: active
version: 1.0.0
tags:
  - vaults
  - defi
  - inspection
tools:
  - codex
  - openclaw
repo_scope:
  - public-vault-api
last_reviewed: 2026-03-24
---

# Inspect Vault

## When To Use

Use this skill to inspect a public IXS tokenized vault and the current wallet position before making any investment decision.

## Inputs

- `IXS_API_BASE_URL`
- `IXS_VAULT_ID`
- wallet from `AGENT_PRIVATE_KEY`

## Trigger Examples

- "Inspect the configured IXS vault and summarize my current wallet position."
- "Before I deposit, check whether this vault looks liquid and show my balances."

## Procedure

1. Call `GET /vaults/{vaultId}`.
2. Call `GET /vaults/{vaultId}/metrics`.
3. Call `GET /vaults/{vaultId}/positions/{walletAddress}`.
4. Summarize:
   - paused state
   - total assets
   - available assets
   - liquidity shortfall or surplus
   - wallet asset balance
   - wallet share balance
   - allowance to vault
   - max withdraw
   - max redeem

## Guardrails

- Do not assume the vault is liquid unless the API data says so.
- Do not invent balances or allowances.
- Stop if the API returns an error.

## References

- `references/api-notes.md`
