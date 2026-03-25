# Wallet And Private-Key Security For Agents

This guide extracts the wallet and signer controls that matter most for agent runtimes.

Use it when:

- an agent can read wallet context
- an agent can sign approvals, deposits, redeems, or other on-chain actions
- an integrator wants a concrete policy for handling `AGENT_PRIVATE_KEY` or any other signer secret

This document is intentionally stricter than a generic app-secret guide because a leaked wallet key usually means direct asset loss.

## Core Policy

Treat wallet private keys as high-impact signing material, not just another environment variable.

Default policy:

1. Prefer external wallet connectors or remote signers with explicit approval UX.
2. Prefer HSM-, KMS-, or MPC-backed signing over raw exported private keys.
3. Use raw private keys only as a compatibility fallback for isolated local testing or tightly scoped single-user runtimes.
4. Never make a raw private key the only supported signer path for a shared or public agent.

## Operating Modes

Do not apply one signing rule to every agent. Separate agents into two operating modes:

### Interactive agents

These are user-facing agents where a human is present at signing time.

Requirements:

- require explicit human confirmation immediately before every signature
- show a pre-sign summary with chain, contract, decoded action, amount, and spender or recipient
- prefer wallet connectors, wallet apps, or hardware-backed signing where the user can approve the final action
- prefer returning unsigned transaction payloads, intent steps, or approval requests to the runtime so the runtime can enforce the final confirmation gate

### Policy-bound autonomous agents

These are non-interactive agents such as automated rebalancers, keepers, relayers, or yield strategies.

They may sign without per-transaction human approval only when all of the following are true:

- the strategy scope is pre-approved and documented
- allowed chains, contracts, methods, and spend bounds are enforced in code or signer policy
- the wallet is dedicated to that automation and holds limited value
- monitoring, alerting, and an emergency stop exist
- the runtime fails closed on policy, decode, quote, RPC, or simulation errors

If those controls are absent, treat the agent as interactive and require per-signature confirmation.

## Preferred Signer Order

Choose signer guidance by operating mode.

For interactive agents:

1. hardware wallet or wallet connector with explicit per-transaction confirmation
2. isolated remote signer backed by KMS, HSM, or MPC with policy controls
3. dedicated hot wallet for low-value execution with strict allowlists
4. raw `AGENT_PRIVATE_KEY` only for isolated test or break-glass compatibility scenarios

For policy-bound autonomous agents:

1. isolated remote signer backed by KMS, HSM, or MPC with policy controls
2. dedicated hot wallet for low-value automated execution with strict allowlists
3. raw `AGENT_PRIVATE_KEY` only for isolated test or break-glass compatibility scenarios

Hardware wallets are strongest for interactive approval flows, but they are usually impractical for server-side automation because they require a physical confirmation step.

## What Agents Must Never Do

- Never ask users to paste a private key, seed phrase, or keystore into chat.
- Never store signing secrets in `SKILL.md`, examples, prompts, transcripts, analytics events, or issue comments.
- Never commit wallet secrets to the repo, including `.env`, `.env.example`, fixtures, or screenshots.
- Never send a raw private key to an external API for quoting, simulation, or transaction construction.
- Never reuse one signer across multiple users, tenants, or public assistants.
- Never use a treasury, admin, deployer, or other privileged wallet as an agent signer.

Exception for sponsor wallets:

- a gas sponsor or relayer wallet may be shared across users only if it cannot approve, transfer, or custody user assets
- sponsor wallets must be isolated from asset-authorizing signers and must have budget, rate, and policy limits

## Runtime Requirements

If an agent runtime can access signing material, require all of the following:

- Inject secrets through a secret manager, orchestrator secret, or equivalent managed channel.
- Keep read-only sessions and execute-capable sessions separate.
- Restrict approved chains, approved RPC hosts, approved API hosts, and approved contracts before the signer is available.
- Require explicit human confirmation immediately before every signature for interactive agents.
- Show a normalized pre-sign summary with chain, target contract, spender or recipient, amount, and decoded action.
- Refuse to sign if the runtime cannot independently verify the target, selector, network, and amount.
- Fail closed on quote, policy, RPC, decode, or simulation errors.
- Mask secrets in logs, traces, crash reports, and debugging output.
- Keep keys only in process memory for as short a time as possible; do not persist them to scratch files, caches, or chat memory.

For policy-bound autonomous agents, replace the per-signature confirmation control with enforced signer policy, monitoring, alerting, and an emergency stop.

Enforcement guidance:

- Prefer runtime-enforced confirmation and policy gates over skill-level pausing logic.
- Skills should prefer preparing unsigned transaction payloads, intent data, or execution plans and returning them to the runtime for final approval, signing, and broadcast.
- Do not rely on prompt text alone to guarantee that a human actually approved the transaction.

## Storage And Injection Rules

Preferred patterns:

- wallet connector or wallet app approval flow
- remote signer service with policy enforcement
- cloud secret manager or KMS-backed injection into a dedicated execution worker

Avoid when a better option exists:

- long-lived secrets in environment variables
- filesystem-mounted secret files
- copying secrets into CI variables that many maintainers can read

If environment injection is unavoidable:

- use a dedicated low-balance wallet
- scope it to one environment and one workflow
- keep the process isolated from general-purpose chat sessions
- rotate immediately after suspected exposure

For a safe local development path, see [`docs/local-sandbox-wallet-quickstart.md`](./local-sandbox-wallet-quickstart.md).

## Wallet Segmentation

Do not let one wallet carry every function.

Minimum segmentation:

- separate read-only agents from signing agents
- separate testnet from mainnet wallets
- separate user-facing automation from treasury or administrative operations
- use low-balance purpose-built wallets for routine agent execution

Use different wallet classes for different duties:

- asset-authorizing signer: can approve, transfer, deposit, redeem, or otherwise move value; do not share across users
- gas sponsor or relayer signer: may pay gas for many users, but must not have authority to move user assets

## Transaction Safety Controls

Private-key security is not enough on its own. Agents also need transaction controls:

- verify the `to` address against an allowlist or trusted metadata
- verify the chain ID matches the configured network
- decode calldata and confirm the selector matches the intended action
- bound approvals and transfer amounts to the user-approved amount
- re-check balances, allowance, and quote validity immediately before signing
- log an auditable transaction summary without exposing secrets

For policy-bound autonomous agents that need dynamic position sizing:

- do not rely on prompt-generated amounts as the only safety control
- prefer contract-level controls such as scoped allowances, vault-specific limits, strategy contracts, or guard contracts that can enforce dynamic bounds on-chain
- use signer policy to narrow where the agent may act, and contract controls to narrow how much value it can move under changing market conditions

## Rotation And Incident Response

Prepare for key compromise before it happens.

Required actions:

- document who can access each signer and for what purpose
- define how the signer is rotated, disabled, and replaced
- alert on unusual signer use, especially from new services or networks
- rotate immediately after any likely exposure in prompts, logs, terminals, or chat history
- move funds out of the compromised wallet if exposure is credible
- review approvals and revoke stale or excessive allowances after an incident

## Publication Rules For Agent Builders

If you publish an execute-capable skill or agent:

- document the supported signer models
- document whether the agent is interactive or policy-bound autonomous
- mark raw private-key support as isolated-test or compatibility-only
- describe the blast radius if the signer is compromised
- document the confirmation step and every pre-sign check
- document approved chains, hosts, contracts, and spending limits

## Approved Signer Interface

Skills should ask for signer capabilities, not raw key material.

At minimum, a runtime should be able to provide:

- `signer_address`: the wallet address the skill is operating against
- `signer_type`: for example `wallet_connector`, `hardware_wallet`, `remote_signer`, `local_private_key`, or `relayer`
- `signer_mode`: `interactive` or `policy_bound_autonomous`
- `approved_chains`: array of allowed chain IDs, for example `[84532]`
- `approved_contracts`: optional allowlist of contracts the signer may touch
- `approved_methods`: optional array of strings containing method selectors such as `0xa9059cbb` or named actions such as `deposit`
- `requires_human_confirmation`: whether per-signature approval is required
- `policy_id` or equivalent session or policy reference for auditability

Runtimes may expose that through environment variables, session state, MCP tools, injected SDK objects, or signed policy tokens. Skills should not require one transport mechanism as long as the capability model is equivalent.

## Quick Checklist

Use this before enabling execution:

- The agent defaults to read-only mode.
- The execution mode is explicit: interactive or policy-bound autonomous.
- The signer is not a treasury or admin wallet.
- Raw private keys are optional, not the default.
- The runtime never asks for secrets in chat.
- Approved hosts, chains, and contracts are enforced.
- Every transaction is decoded and summarized before signing.
- Logs and telemetry cannot expose wallet secrets.
- Rotation and revocation steps are documented.

## Sources

This guide was informed by the following references reviewed on March 25, 2026:

- [OWASP Secrets Management Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Secrets_Management_Cheat_Sheet.html)
- [Google Cloud Secret Manager best practices](https://cloud.google.com/secret-manager/docs/best-practices)
- [AWS KMS IAM best practices](https://docs.aws.amazon.com/prescriptive-guidance/latest/aws-kms-best-practices/access.html)
- [AWS KMS key management best practices](https://docs.aws.amazon.com/prescriptive-guidance/latest/aws-kms-best-practices/key-management.html)
- [MetaMask basic safety and security tips](https://support.metamask.io/stay-safe/safety-in-web3/basic-safety-and-security-tips-for-metamask)
