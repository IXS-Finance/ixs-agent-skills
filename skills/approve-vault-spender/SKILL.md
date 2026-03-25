---
name: approve-vault-spender
description: Plan or execute the ERC-20 approval step required before depositing into a public IXS ERC-4626 vault.
owner: IXS Protocol
status: active
version: 1.0.1
tags:
  - vaults
  - erc20
  - approval
tools:
  - codex
  - openclaw
repo_scope:
  - public-erc20
  - public-erc4626-vault
last_reviewed: 2026-03-25
---

# Approve Vault Spender

## When To Use

Use this skill when a deposit requires an ERC-20 approval step before the vault can transfer assets from the wallet.

## Inputs

- asset token address
- `IXS_VAULT_ADDRESS`
- wallet from an approved signer; use `AGENT_PRIVATE_KEY` only for isolated test or compatibility setups
- target approval amount
- `--execute` to actually send the approval transaction

## Trigger Examples

- "Approve the vault to spend 1,000 USDC from my wallet."
- "Plan the approval step for a vault deposit without executing it."

## Procedure

1. Read current `allowance(owner, vaultAddress)`.
2. Compare the allowance to the requested approval target.
3. If not in execute mode, print the current allowance and the approval amount needed.
4. If in execute mode, call `approve(vaultAddress, amount)` on the asset token.
5. Wait for confirmation, then re-read allowance and report the updated value.

## Guardrails

- Do not send an approval transaction if the current allowance is already sufficient unless explicitly asked.
- Never invent token, spender, or calldata values.
- Stop if the approval transaction fails or the post-transaction allowance does not update as expected.

## References

- `references/approval-flow.md`
