# Comparison Surface

This skill combines the main read-only ERC-4626 decision points already surfaced in the IXS UI:

- deposit preview
- redeem preview
- redeem fee preview (`ManagedVault`-kind only)
- max deposit and max redeem
- wallet balances and allowance
- total and available assets

## Async Vault Caveat

On `ERC7540OperatedVault`-kind vaults: `maxDeposit`/`maxMint`/`maxWithdraw`/`maxRedeem` always return `0` (sync entry points disabled by design, not a real block), there is no separate redeem-fee preview (fee is already netted into `previewRedeem`), and `previewDeposit`/`previewRedeem` are non-binding estimates rather than the price that will actually apply. Read `settlement` from `vault_get` first and adjust which of the above to call and how to describe the results.
