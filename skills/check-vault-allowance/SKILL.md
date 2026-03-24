---
name: check-vault-allowance
description: Inspect a wallet's ERC-20 allowance to the vault and determine whether approval is required for a target deposit amount.
owner: IXS Protocol
status: active
version: 1.0.0
tags:
  - vaults
  - erc20
  - allowance
tools:
  - codex
  - openclaw
repo_scope:
  - public-erc20
  - public-erc4626-vault
last_reviewed: 2026-03-24
---

# Check Vault Allowance

## When To Use

Use this skill before deposit planning or execution to determine whether the wallet already has enough ERC-20 allowance for the vault.

## Inputs

- `IXS_VAULT_ADDRESS`
- asset token address
- wallet from `AGENT_PRIVATE_KEY`
- target deposit asset amount

## Trigger Examples

- "Check whether my wallet has enough allowance to deposit 250 USDC into the vault."
- "Do I need to approve this vault before depositing?"

## Procedure

1. Read `allowance(owner, vaultAddress)` from the asset token.
2. Read `balanceOf(owner)` from the asset token.
3. Compare allowance and balance to the target deposit amount.
4. Summarize:
   - current allowance
   - current wallet asset balance
   - target deposit amount
   - whether approval is required
   - whether balance is sufficient

## Guardrails

- Never assume approval is unnecessary without reading the current allowance.
- Keep allowance sufficiency separate from balance sufficiency.
- Stop if the token read calls fail.

## References

- `references/approval-flow.md`
