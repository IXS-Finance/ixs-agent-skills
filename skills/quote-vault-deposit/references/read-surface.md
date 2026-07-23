# Read Surface

Relevant ERC-4626 read methods in IXS UI:

- `totalAssets()`
- `availableAssets()`
- `maxDeposit(receiver)`
- `previewDeposit(assets)`

These are exposed in the shared ERC-4626 ABI used by the UI.

## Async Vault Caveat

On `ERC7540OperatedVault`-kind vaults, `maxDeposit`/`maxMint`/`maxWithdraw`/`maxRedeem` are hardcoded to always return `0` — the vault's synchronous ERC-4626 entry points are disabled by design, not because deposits/redeems are actually blocked. `previewDeposit`/`previewWithdraw`/`previewRedeem` still work but are documented in the contract itself as "estimate only... not binding" — the real conversion rate is set when the request is later finalized, at whatever price applies then. Get `settlement` from `vault_get` before interpreting any of these reads.
