# API Notes

This skill depends on the public vault API exposing:

- `GET /vaults/{vaultId}`
- `GET /vaults/{vaultId}/metrics`
- `GET /vaults/{vaultId}/positions/{walletAddress}`

Wallet-aware summaries should be based on API responses only.
