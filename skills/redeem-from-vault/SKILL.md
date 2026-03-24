---
name: redeem-from-vault
description: Plan or execute a redeem from a public IXS tokenized vault using quote and intent API endpoints.
owner: IXS Protocol
status: active
version: 1.0.0
tags:
  - vaults
  - defi
  - redeem
tools:
  - codex
  - openclaw
repo_scope:
  - public-vault-api
last_reviewed: 2026-03-24
---

# Redeem From Vault

## When To Use

Use this skill to plan or execute a redeem from the public IXS tokenized vault using the HTTP API.

## Inputs

- `IXS_API_BASE_URL`
- `IXS_VAULT_ID`
- wallet from `AGENT_PRIVATE_KEY`
- `AGENT_REDEEM_SHARE_AMOUNT`
- `AGENT_MIN_SHARE_BALANCE_TO_REDEEM`
- `--execute` to actually send transactions

## Trigger Examples

- "Quote a redeem from the configured vault without broadcasting transactions."
- "Redeem shares only if the wallet balance is sufficient and liquidity still looks reasonable."

## Procedure

1. Call `GET /vaults/{vaultId}/metrics`.
2. Call `GET /vaults/{vaultId}/positions/{walletAddress}`.
3. Call `POST /vaults/{vaultId}/quote-redeem`.
4. Call `POST /vaults/{vaultId}/intents/redeem`.
5. Check:
   - wallet share balance is enough
   - current liquidity looks reasonable
6. If not in execute mode, stop after printing the quote and intent.
7. If in execute mode, send the returned redeem transaction.

## Guardrails

- Never redeem more shares than the wallet holds.
- Treat liquidity shortfall as an execution risk.
- Never invent calldata. Use the returned intent step exactly.

## References

- `references/liquidity-risk.md`
