# API Notes

This skill depends on:

- MCP tool `vault_get` (via `POST /mcp`) — vault metadata and settlement kind (`sync` vs `async-erc7540`), detected by the API from the deployed contract's ERC-165 interface support, not from a static field. Trust the tool's answer over any assumption about which contract a vault uses.
- `GET /vaults/{vaultId}/positions/{walletAddress}` — wallet balances, allowance, and limits.
- MCP tool `vault_request_status` (async vaults only) — a wallet's recent deposit/redeem requests and their claimable state, sourced from the vault subgraph.

`GET /vaults/{vaultId}/metrics` does not exist on the current API. Vault-level totals (`totalAssets`, `availableAssets`, `paused`) are read directly from the vault contract, not proxied through REST.

Wallet-aware summaries should be based on tool/API responses and direct contract reads only — never invented.
