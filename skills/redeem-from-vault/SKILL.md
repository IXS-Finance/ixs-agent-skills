---
name: redeem-from-vault
description: Plan or execute a redeem from a public IXS tokenized vault using MCP tool calls.
owner: IXS Protocol
status: active
version: 2.0.0
tags:
  - vaults
  - defi
  - redeem
  - mcp
tools:
  - codex
  - openclaw
repo_scope:
  - public-vault-mcp
last_reviewed: 2026-07-23
---

# Redeem From Vault

## When To Use

Use this skill to plan or execute a redeem from a public IXS tokenized vault via its MCP server.

## Inputs

- `IXS_MCP_URL`
- `IXS_API_BASE_URL`
- `IXS_VAULT_ID`
- wallet from an approved signer; use `AGENT_PRIVATE_KEY` only for isolated test or compatibility setups
- `AGENT_REDEEM_SHARE_AMOUNT`
- `AGENT_MIN_SHARE_BALANCE_TO_REDEEM`
- `--execute` to actually send transactions

## Trigger Examples

- "Plan a redeem from the configured vault without broadcasting transactions."
- "Redeem shares only if the wallet balance is sufficient and liquidity still looks reasonable."

## Procedure

1. Call the MCP tool `vault_get` with `vaultId`. Read `settlement` (`sync` or `async-erc7540`) for context, though redeeming is never instantaneous on either vault kind (see below).
2. Call `GET /vaults/{vaultId}/positions/{walletAddress}` for the wallet's current share balance and redeem limits.
3. Call the MCP tool `vault_build_request_redeem` with `vaultId`, `ownerAddress`, and `shareAmount`. It returns an unsigned `vault_request_redeem` transaction step and its own `settlement` value: `queued` (`ManagedVault`-kind vaults — this is the vault's only redeem path, and it always queues) or `async-erc7540` (`ERC7540OperatedVault`-kind vaults).
4. Check:
   - wallet share balance is enough
   - current liquidity looks reasonable
5. If not in execute mode, stop after printing the returned step and `settlement`.
6. If in execute mode, send the returned transaction.
7. Redeeming is never final in a single transaction on either vault kind:
   - `queued` — the vault finalizes the request on its own after this call; there is no separate claim step to send. Do not imply the assets are already in the wallet.
   - `async-erc7540` — poll the MCP tool `vault_request_status` for the wallet until the request shows a claimable amount, then call the MCP tool `vault_build_claim_redeem` with `vaultId`, `ownerAddress`, and the request's `requestId`, and send its returned transaction to actually receive assets.

## Guardrails

- Never redeem more shares than the wallet holds.
- Treat liquidity shortfall as an execution risk.
- Never invent calldata. Use the returned MCP tool steps exactly.
- Never report a redeem as settled just because `vault_build_request_redeem`'s transaction was sent — it only queues shares. For `async-erc7540` vaults specifically, confirm the claim step (step 7) has also been executed before reporting assets received.
- `POST /vaults/{vaultId}/quote-redeem` and `POST /vaults/{vaultId}/intents/redeem` do not exist on the current API — do not call them. This flow goes through MCP only.

## References

- `references/liquidity-risk.md`
