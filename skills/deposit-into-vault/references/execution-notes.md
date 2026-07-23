# Execution Notes

This skill should distinguish planning from execution clearly:

- planning mode prints the MCP tool response only
- execution mode follows the returned transaction steps in order

Returned calldata and transaction parameters must come from the `vault_build_request_deposit` / `vault_build_claim_deposit` responses without manual reconstruction.

## Sync vs Async Settlement

`vault_get` and `vault_build_request_deposit` both return `settlement`. Treat it as authoritative, not something to infer from the vault's name or id:

- `sync` (`ManagedVault`-kind vaults) — the deposit transaction from `vault_build_request_deposit` is the whole flow. Shares are minted in that same transaction.
- `async-erc7540` (`ERC7540OperatedVault`-kind vaults) — the deposit transaction only queues a request. A second transaction, from `vault_build_claim_deposit`, is required to actually receive shares, and it can only be sent once `vault_request_status` (or `claimableDepositRequest` on-chain) shows a claimable amount for that `requestId`. Do not report the deposit as complete until that second transaction has been sent and confirmed.
