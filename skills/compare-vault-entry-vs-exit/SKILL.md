---
name: compare-vault-entry-vs-exit
description: Compare deposit and redeem conditions for a wallet on a public IXS ERC-4626 vault before taking action.
owner: IXS Protocol
status: active
version: 1.1.0
tags:
  - vaults
  - analysis
  - decision-support
  - mcp
tools:
  - codex
  - openclaw
repo_scope:
  - public-erc4626-vault
  - public-vault-mcp
last_reviewed: 2026-07-23
---

# Compare Vault Entry Vs Exit

## When To Use

Use this skill when the user wants to compare deposit and redeem conditions before deciding whether to enter, add to, or exit a public IXS ERC-4626 vault position.

## Inputs

- `IXS_MCP_URL`
- `IXS_VAULT_ADDRESS`
- `IXS_VAULT_CHAIN_ID`
- wallet from an approved signer; use `AGENT_PRIVATE_KEY` only for isolated test or compatibility setups
- target deposit asset amount
- target redeem share amount

## Trigger Examples

- "Compare whether it looks better to deposit more or redeem right now."
- "Show me the current deposit and redeem conditions for this vault and my wallet."

## Procedure

1. Call the MCP tool `vault_get` first and read `settlement` (`sync` for `ManagedVault`-kind vaults, `async-erc7540` for `ERC7540OperatedVault`-kind vaults) — it changes how to interpret every read below.
2. Read vault state needed for both entry and exit:
   - `paused()`
   - `totalAssets()`
   - `availableAssets()`
   - `maxDeposit(receiver)`
   - `maxRedeem(owner)`
3. Read wallet state:
   - asset balance
   - share balance
   - allowance to the vault
4. Read quote surfaces:
   - `previewDeposit(assets)`
   - `previewRedeem(shares)`
   - `previewRedeemFee(shares)` — `ManagedVault`-kind only; do not call this on `async-erc7540` vaults, it does not exist there
5. Summarize:
   - whether deposit is currently allowed and sufficiently funded
   - whether redeem is currently within wallet and vault limits
   - expected deposit shares out
   - expected redeem assets out and fee (net-of-fee only, no separate fee figure, on `async-erc7540` vaults)
   - allowance and liquidity constraints
6. Highlight which blockers apply to entry, exit, or both — on `async-erc7540` vaults, `maxDeposit`/`maxRedeem` always read `0` by design (the sync path is disabled, not blocked); say so instead of reporting entry/exit as unavailable.

## Guardrails

- Do not recommend an investment action as financial advice.
- Keep read-only comparison separate from execution planning.
- Call out uncertainty when wallet-specific inputs are missing.
- On `async-erc7540` vaults, treat `previewDeposit`/`previewRedeem` as non-binding estimates, not the figures that will actually apply — the real price is set when the corresponding request is finalized.

## References

- `references/comparison-surface.md`
