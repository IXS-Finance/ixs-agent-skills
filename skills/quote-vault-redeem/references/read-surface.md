# Read Surface

Relevant ERC-4626 read methods in IXS UI:

- `balanceOf(owner)`
- `maxRedeem(owner)`
- `previewRedeem(shares)`
- `previewRedeemFee(shares)` — `ManagedVault`-kind only, see caveat below
- `totalAssets()`
- `availableAssets()`

## Async Vault Caveat

`ERC7540OperatedVault`-kind vaults hardcode `maxDeposit`/`maxMint`/`maxWithdraw`/`maxRedeem` to always return `0` (synchronous entry points are disabled by design) and do not implement `previewRedeemFee`/`previewWithdrawFee`/`previewDepositFee` at all — fee is already netted into `previewRedeem`/`previewWithdraw`/`previewDeposit`. Calling `previewRedeemFee` on one of these vaults will revert. `previewRedeem` itself still works but is documented in the contract as "estimate only... not binding" — the real conversion rate is set when the request is finalized. Get `settlement` from `vault_get` before choosing which reads to make.
