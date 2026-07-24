# History Sources

IXS currently models vault history from more than one source:

- the MCP tool `vault_request_status` — subgraph-backed request history (deposit/redeem requests, status, claimable state), the canonical source for both `ManagedVault`- and `ERC7540OperatedVault`-kind async flows
- persisted local ERC-4626 wallet history in the UI runtime

History summaries should state which source was used.
