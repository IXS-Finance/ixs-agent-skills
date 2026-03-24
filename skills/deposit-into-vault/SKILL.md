---
name: deposit-into-vault
description: Plan or execute a deposit into a public IXS tokenized vault using quote and intent API endpoints.
owner: IXS Protocol
status: active
version: 1.0.0
tags:
  - vaults
  - defi
  - deposits
tools:
  - codex
  - openclaw
repo_scope:
  - public-vault-api
last_reviewed: 2026-03-24
---

# Deposit Into Vault

## When To Use

Use this skill to plan or execute a deposit into the public IXS tokenized vault using the HTTP API.

## Inputs

- `IXS_API_BASE_URL`
- `IXS_VAULT_ID`
- wallet from `AGENT_PRIVATE_KEY`
- `AGENT_DEPOSIT_ASSET_AMOUNT`
- `AGENT_MIN_ASSET_BALANCE_TO_DEPOSIT`
- `--execute` to actually send transactions

## Trigger Examples

- "Quote a deposit into the configured IXS vault without executing it."
- "Deposit into the vault only if the quote is executable now and the allowance is already sufficient."

## Procedure

1. Call `GET /vaults/{vaultId}/metrics`.
2. Call `GET /vaults/{vaultId}/positions/{walletAddress}`.
3. Call `POST /vaults/{vaultId}/quote-deposit`.
4. Call `POST /vaults/{vaultId}/intents/deposit`.
5. Check:
   - wallet asset balance is enough
   - `quote.executableNow` is `true`
   - current allowance is enough, otherwise send the approve step first
6. If not in execute mode, stop after printing the quote and intent.
7. If in execute mode, send the returned transaction steps in order.

## Guardrails

- Never deposit more than the wallet asset balance.
- Never skip the allowance check.
- Never invent calldata. Use the returned intent steps exactly.
- Do not execute when the quote says it is not executable now.

## References

- `references/execution-notes.md`
