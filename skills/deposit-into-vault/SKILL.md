---
name: deposit-into-vault
description: Plan or execute a deposit into a public IXS tokenized vault using MCP tool calls.
owner: IXS Protocol
status: active
version: 2.0.0
tags:
  - vaults
  - defi
  - deposits
  - mcp
tools:
  - codex
  - openclaw
repo_scope:
  - public-vault-mcp
last_reviewed: 2026-07-23
---

# Deposit Into Vault

## When To Use

Use this skill to plan or execute a deposit into a public IXS tokenized vault via its MCP server.

## Inputs

- `IXS_MCP_URL`
- `IXS_API_BASE_URL`
- `IXS_VAULT_ID`
- wallet from an approved signer; use `AGENT_PRIVATE_KEY` only for isolated test or compatibility setups
- `AGENT_DEPOSIT_ASSET_AMOUNT`
- `AGENT_MIN_ASSET_BALANCE_TO_DEPOSIT`
- `--execute` to actually send transactions

## Trigger Examples

- "Plan a deposit into the configured IXS vault without executing it."
- "Deposit into the vault only if the wallet balance and allowance are already sufficient."

## Procedure

1. Call the MCP tool `vault_get` with `vaultId`. Read `settlement` (`sync` or `async-erc7540`) — it determines what happens after the deposit transaction lands.
2. Call `GET /vaults/{vaultId}/positions/{walletAddress}` for the wallet's current asset balance and allowance to the vault.
3. Call the MCP tool `vault_build_request_deposit` with `vaultId`, `ownerAddress`, and `assetAmount`. It returns unsigned transaction steps (an `erc20_approve_exact` step plus either a `vault_deposit` or `vault_request_deposit` step, matching `settlement`) and echoes `settlement` again in the response.
4. Check:
   - wallet asset balance is enough
   - current allowance already covers the amount — if so, the approve step from the tool response can be skipped
5. If not in execute mode, stop after printing the returned steps and `settlement`.
6. If in execute mode, send the returned transaction steps in order, exactly as given.
7. If `settlement` is `sync`, the deposit is final once that transaction confirms.
8. If `settlement` is `async-erc7540`, the deposit is only queued once that transaction confirms — it has not settled yet. Poll the MCP tool `vault_request_status` for the wallet until the request shows a claimable amount, then call the MCP tool `vault_build_claim_deposit` with `vaultId`, `ownerAddress`, and the request's `requestId`, and send its returned transaction to actually receive shares.

## Guardrails

- Never deposit more than the wallet asset balance.
- Never skip the allowance check.
- Never invent calldata. Use the returned MCP tool steps exactly.
- Never assume a deposit is complete just because `vault_build_request_deposit`'s transaction was sent — check `settlement` and, for async vaults, confirm the claim step (step 8) has also been executed before reporting shares received.
- `POST /vaults/{vaultId}/quote-deposit` and `POST /vaults/{vaultId}/intents/deposit` do not exist on the current API — do not call them. This flow goes through MCP only.

## References

- `references/execution-notes.md`
