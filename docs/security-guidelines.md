# Security Guidelines For Users And Builders

## Purpose

This repository contains agent skills for inspecting vaults, checking allowances, previewing deposit and redeem flows, and in some cases executing on-chain actions. Because these skills can influence wallet behavior, they should be treated as privileged operational logic rather than ordinary prompt snippets.

For focused signer and wallet-secret guidance, see [`docs/wallet-private-key-security.md`](./wallet-private-key-security.md).

This guide is written for two audiences:

- end users who want to run these skills safely
- builders who want to publish agents or extend these skills for others

## Security Principles

If you use or build on these skills, follow these rules:

1. Default to read-only behavior. Quotes, inspection, and comparison should be the default. Execution should be opt-in.
2. Never sign a transaction you cannot independently explain. The agent should verify chain, contract, amount, and recipient before execution.
3. Keep signing authority isolated. Use a dedicated wallet with limited funds for agent-driven execution.
4. Treat API responses, runtime configuration, and prompt input as untrusted until verified.
5. Publish execute-capable skills only with clear guardrails, human confirmation, and narrow scope.

## Guidance For End Users

### Safe setup

- Use a dedicated low-balance wallet for any execution-capable agent.
- Do not load a production treasury or primary wallet into a general-purpose chat runtime.
- Prefer a signer or wallet provider with explicit transaction prompts over raw private-key environment variables when your runtime supports it.
- Restrict the API and RPC hosts your agent is allowed to call.
- Separate read-only environments from execution environments whenever possible.

### Before you allow execution

- Confirm the chain ID matches the network you intend to use.
- Confirm the vault address and asset token address are the expected contracts.
- Confirm the approval spender is the expected vault contract.
- Confirm the amount shown in the transaction matches the amount you requested.
- Confirm the recipient and any calldata-derived action match the intended operation.

### During normal use

- Start with `inspect`, `quote`, or `compare` flows before any approval, deposit, or redeem action.
- Treat approvals as separate high-risk actions. Approve only the amount you need unless you have a deliberate reason to do otherwise.
- Re-check allowance, balances, and quote conditions immediately before execution.
- If the quote says execution is not currently safe or executable, stop and reassess.
- Revoke or reduce approvals that are no longer needed.

### When to stop immediately

- The runtime cannot explain what a transaction will do in plain language.
- The API response asks the agent to sign a transaction for an unexpected address or network.
- The displayed amount, asset, or spender differs from your request.
- The skill skips a balance, allowance, or liquidity check.
- The transaction depends on an endpoint or host you did not approve.

## Guidance For Builders

### Required behavior for execute-capable agents

If your agent can approve, deposit, redeem, or otherwise write state:

- Default to plan mode, not execute mode.
- Classify the agent as either interactive or policy-bound autonomous.
- Require an explicit confirmation step immediately before signing for interactive agents.
- Prefer runtime-enforced confirmation over skill-level interactive pauses.
- Show a normalized pre-sign summary:
  - chain ID
  - target contract
  - token or share amount
  - spender or recipient
  - expected action
- Reject execution when the transaction target, selector, amount, or network cannot be independently verified.
- For policy-bound autonomous agents, enforce approved chains, contracts, methods, and spend bounds without fallback to prompt-only checks.
- For dynamic autonomous sizing, pair signer policy with contract-level controls such as scoped allowances or strategy-specific guard contracts.
- Log enough detail for a user to audit what the agent attempted.

### Safe transaction construction

- Do not trust API-returned transaction steps blindly.
- Verify the `to` address against expected vault or token metadata.
- Verify the function selector or decoded action matches the intended operation.
- Verify the chain matches the configured network.
- Treat any configurable API base URL as untrusted unless it is pinned to an approved HTTPS host.

### Wallet and signer handling

Keep the detailed wallet-secret policy in [`docs/wallet-private-key-security.md`](./wallet-private-key-security.md) and the local dev exception path in [`docs/local-sandbox-wallet-quickstart.md`](./local-sandbox-wallet-quickstart.md).

At a minimum:

- do not assume `AGENT_PRIVATE_KEY` is an acceptable default for all deployments
- prefer wallet connectors or managed signer services over raw private keys
- never ask users to paste secrets into chat or prompt text
- never print secrets, derived keys, or sensitive wallet material into logs
- document the signer model, blast radius, and confirmation or policy controls for every execute-capable agent

### Publication and review requirements

Before publishing an `active` skill that can execute:

- document the exact assets and contracts it can touch
- document all required checks before signing
- document what happens in planning mode versus execute mode
- document how a user can confirm, cancel, or recover from a failed step
- mark any dependency on private APIs, runtime history stores, or adjacent repos clearly

Recommended metadata for published execute-capable skills:

- `risk_level`
- `execution_capability`
- `requires_human_confirmation`
- `approved_hosts`
- `approved_chains`

## Repository-Specific Notes

These recommendations are especially important for this repo because several skills already describe live transaction flows.

### API-driven execution flows

The deposit and redeem skills currently instruct runtimes to fetch quote and intent data, then execute returned transaction steps:

- [skills/deposit-into-vault/SKILL.md](../skills/deposit-into-vault/SKILL.md#L41)
- [skills/deposit-into-vault/SKILL.md](../skills/deposit-into-vault/SKILL.md#L50)
- [skills/redeem-from-vault/SKILL.md](../skills/redeem-from-vault/SKILL.md#L41)
- [skills/redeem-from-vault/SKILL.md](../skills/redeem-from-vault/SKILL.md#L49)

If you build on these skills, add an independent verification layer before any signing step. A compromised API, wrong base URL, or prompt-level misconfiguration should not be able to redirect user funds.

Where possible, have the skill produce unsigned transaction payloads or intent data and let the runtime own final confirmation, signing, and broadcast.

### Approval flows

The approval skill correctly separates planning from execution, but approvals remain high-risk because they expand another contract's ability to move assets:

- [skills/approve-vault-spender/SKILL.md](../skills/approve-vault-spender/SKILL.md#L41)
- [skills/approve-vault-spender/SKILL.md](../skills/approve-vault-spender/SKILL.md#L44)

Builders should add explicit spender verification and end users should prefer minimal approval amounts.

### Private-key-driven wallet context

Several skills support wallet context via `AGENT_PRIVATE_KEY`:

- [skills/deposit-into-vault/SKILL.md](../skills/deposit-into-vault/SKILL.md#L29)
- [skills/redeem-from-vault/SKILL.md](../skills/redeem-from-vault/SKILL.md#L29)
- [skills/approve-vault-spender/SKILL.md](../skills/approve-vault-spender/SKILL.md#L30)

That may be acceptable for isolated testing, but builders should make the deployment stance explicit:

- acceptable: local development, disposable test wallets, isolated single-user sandboxes
- not acceptable: shared chat agents, public-facing assistants, team wallets, or production treasury operations

If you keep `AGENT_PRIVATE_KEY` support for compatibility, document it as an unsafe-by-default fallback and pair it with an approved-signer alternative.

### Current governance gap

The repository's governance and authoring docs currently focus on structure and metadata, not execution safety:

- [docs/governance.md](./governance.md#L7)
- [docs/governance.md](./governance.md#L14)
- [docs/governance.md](./governance.md#L36)
- [docs/skill-authoring-guidelines.md](./skill-authoring-guidelines.md#L31)

If you publish agents or reuse these skills in production, add your own security review gate rather than relying on structural validation alone.

## Recommended Publish Checklist

Use this checklist before publishing an agent built on this repo:

- The agent has a read-only default mode.
- Every execute path has a pre-sign verification step.
- Every interactive execute path requires explicit human confirmation.
- Every policy-bound autonomous path has enforced chain, contract, method, and spend policy.
- The signer policy follows [`docs/wallet-private-key-security.md`](./wallet-private-key-security.md).
- Approved hosts and supported chains are documented and enforced.
- Approval flows are bounded and explain spender risk clearly.
- Secrets are isolated from general-purpose chat sessions.
- Logs avoid exposing sensitive wallet material.
- Failure paths are safe and do not retry destructive actions automatically.
- The agent explains what it is about to sign in plain language.

## What This Repository Can Safely Promise

- It provides a useful starting point for vault-oriented agent skills.
- It separates many read-only workflows from execution workflows.
- It already includes some basic operational guardrails such as balance, allowance, and liquidity checks.

## What This Repository Should Not Promise

- That API-returned transaction steps are safe without verification.
- That a general-purpose agent runtime is a safe home for production signing keys.
- That structural CI checks are enough to approve execute-capable skills for public deployment.

## Final Recommendation

Use this repository freely for read-only inspection, quoting, and comparison use cases.

For any public or shared agent that can execute approvals, deposits, or redeems, add a separate security control layer around these skills: host allowlisting, transaction decoding and verification, explicit human confirmation, isolated signing, and a stronger publication review gate. Without those controls, the main risk is not just software bugs, but an agent being induced to sign the wrong transaction under apparently normal conditions.
