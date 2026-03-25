# Integrator Notes

This repo is meant for agent runtimes that can load local skill folders and execute a mix of read-only calls, API calls, and wallet-backed EVM transactions.

## Runtime Expectations

A compatible runtime should be able to:

- discover skills from a filesystem path
- treat `SKILL.md` as the skill entrypoint
- provide environment variables to the agent
- make HTTP requests to `ixs-api` when a skill depends on API endpoints
- sign and broadcast EVM transactions when a skill is used in execute mode
- provide signer capabilities and wallet address without requiring raw private-key access inside the skill prompt

## Read-Only Versus Execute Mode

Read-only skills:

- `inspect-vault`
- `quote-vault-deposit`
- `quote-vault-redeem`
- `check-vault-allowance`
- `review-vault-wallet-history`
- `compare-vault-entry-vs-exit`

Skills that may execute transactions:

- `approve-vault-spender`
- `deposit-into-vault`
- `redeem-from-vault`

Execution should happen only when the caller explicitly opts into execution. Read-only output should never imply that funds were moved.

## Address And Network Defaults

These values are grounded in the current IXS codebase and were verified against the live API and chain reads during repo setup:

- chain: Base Sepolia
- chain id: `84532`
- API vault id: `ixs-tokenized-vault-base-sepolia`
- ERC-4626 vault: `0x9421a6C925D466Ac22956B1a7D553c3E74F59571`
- shared USDC asset: `0xbBCa80a7116aE46B0f249D279EF43f86274dc4f4`
- API base URL: `https://api-dev-v2.ixs.finance`
- MCP URL: `https://api-dev-v2.ixs.finance/mcp`
- Base Sepolia RPC default: `https://sepolia.base.org`

## API And On-Chain Split

This repo contains two execution surfaces:

- API-driven vault flows that use `ixs-api` quote and intent endpoints
- ERC-4626 read and wallet flows based on on-chain contract reads and transactions

Integrators should keep those surfaces distinct in their runtime:

- read-only contract calls should be cheap and repeatable
- API quote and intent calls should be treated as ephemeral planning data
- transaction execution should use the exact returned intent steps or exact contract calldata the skill describes

## Wallet Safety

For a focused signer policy, see [`docs/wallet-private-key-security.md`](./wallet-private-key-security.md).

Recommended runtime behavior:

- require an explicit execute flag or separate approval step before any on-chain action
- surface spender, vault, chain, and amount before signing
- enforce human confirmation or signer policy in the runtime orchestration layer, not only in skill text
- re-read allowance or balances after approval when the workflow requires it
- fail closed on API or RPC errors
- prefer external wallet connectors or signer services over raw `AGENT_PRIVATE_KEY`
- if `AGENT_PRIVATE_KEY` is supported, inject it only through secret management, never through chat or prompt text
- keep signer-enabled runtimes separate from read-only runtimes
- mask secrets in logs, traces, and crash output
- refuse to sign for unapproved chains, hosts, or contracts even when a key is loaded

Recommended signer capability surface:

- `signer_address`
- `signer_type`
- `signer_mode`
- `approved_chains`
- optional `approved_contracts`
- optional `approved_methods`
- `requires_human_confirmation`
- `policy_id` or equivalent audit reference

Runtimes may expose those capabilities through environment variables, session state, MCP tools, SDK injection, or policy tokens. The important part is that skills receive signer scope and wallet context without needing raw key material in the prompt surface.

When a skill can return unsigned transaction payloads, intent data, or execution plans, prefer that pattern over having the skill directly sign and broadcast.

## History Caveat

Vault history may come from different sources depending on the runtime:

- subgraph-backed history
- runtime-persisted ERC-4626 wallet history

Skills should state which source they used and should not imply completeness unless the source is authoritative for that runtime.
