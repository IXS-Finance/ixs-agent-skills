# Liquidity Risk Notes

Redeem execution should consider available assets and any reported liquidity shortfall — read `availableAssets()` directly from the vault contract (see `inspect-vault`) since it is not part of the `vault_get` or `vault_build_request_redeem` MCP responses.

If on-chain reads indicate immediate execution is risky, the skill should surface that risk clearly before proceeding.

## Settlement Is Never Instant

Both vault kinds queue redeems — there is no vault variant where `vault_build_request_redeem`'s transaction alone moves assets to the wallet:

- `ManagedVault`-kind vaults (`settlement: "queued"`) finalize the request on their own; there is nothing further for the skill or the caller to send.
- `ERC7540OperatedVault`-kind vaults (`settlement: "async-erc7540"`) require an explicit claim transaction from `vault_build_claim_redeem` once `vault_request_status` shows the request as claimable.

Report a redeem as "requested" immediately after step 6, and only report it as "settled"/"assets received" once the vault-appropriate finalization above has actually happened.
