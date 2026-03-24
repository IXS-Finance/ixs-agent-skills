---
name: compare-vault-entry-vs-exit
description: Compare deposit and redeem conditions for a wallet on a public IXS ERC-4626 vault before taking action.
owner: IXS Protocol
status: active
version: 1.0.0
tags:
  - vaults
  - analysis
  - decision-support
tools:
  - codex
  - openclaw
repo_scope:
  - public-erc4626-vault
last_reviewed: 2026-03-24
---

# Compare Vault Entry Vs Exit

## When To Use

Use this skill when the user wants to compare deposit and redeem conditions before deciding whether to enter, add to, or exit a public IXS ERC-4626 vault position.

## Inputs

- `IXS_VAULT_ADDRESS`
- `IXS_VAULT_CHAIN_ID`
- wallet from `AGENT_PRIVATE_KEY`
- target deposit asset amount
- target redeem share amount

## Trigger Examples

- "Compare whether it looks better to deposit more or redeem right now."
- "Show me the current deposit and redeem conditions for this vault and my wallet."

## Procedure

1. Read vault state needed for both entry and exit:
   - `paused()`
   - `totalAssets()`
   - `availableAssets()`
   - `maxDeposit(receiver)`
   - `maxRedeem(owner)`
2. Read wallet state:
   - asset balance
   - share balance
   - allowance to the vault
3. Read quote surfaces:
   - `previewDeposit(assets)`
   - `previewRedeem(shares)`
   - `previewRedeemFee(shares)`
4. Summarize:
   - whether deposit is currently allowed and sufficiently funded
   - whether redeem is currently within wallet and vault limits
   - expected deposit shares out
   - expected redeem assets out and fee
   - allowance and liquidity constraints
5. Highlight which blockers apply to entry, exit, or both.

## Guardrails

- Do not recommend an investment action as financial advice.
- Keep read-only comparison separate from execution planning.
- Call out uncertainty when wallet-specific inputs are missing.

## References

- `references/comparison-surface.md`
