# ERC-4626 Happy Path

This is the simplest end-to-end flow for an agent runtime integrating the public IXS ERC-4626 vault skills.

## Inputs

- `IXS_VAULT_ID=ixs-tokenized-vault-base-sepolia`
- `IXS_VAULT_CHAIN_ID=84532`
- `IXS_VAULT_ADDRESS=0x9421a6C925D466Ac22956B1a7D553c3E74F59571`
- `IXS_ASSET_ADDRESS=0xbBCa80a7116aE46B0f249D279EF43f86274dc4f4`
- `AGENT_PRIVATE_KEY`

## Flow

1. Run `inspect-vault`
   - confirm paused state, total assets, available assets, and wallet balances
2. Run `quote-vault-deposit`
   - estimate expected shares for the target asset amount
3. Run `check-vault-allowance`
   - verify whether approval is needed for that amount
4. If approval is needed, run `approve-vault-spender --execute`
   - approve the target amount to the vault
5. Run `deposit-into-vault --execute`
   - send the returned transaction steps in order
6. Run `review-vault-wallet-history`
   - confirm the new activity is reflected in the wallet feed

## Example Prompt Sequence

- "Inspect the configured IXS vault and summarize my wallet position."
- "Quote a 250 USDC deposit."
- "Check whether my wallet allowance is enough for that deposit."
- "Approve the vault to spend 250 USDC."
- "Execute the deposit using the configured vault."
- "Review my recent vault activity."
