# Private Key Guidelines Review Notes

## Overview of Changes
- Added a new, comprehensive guide: `docs/wallet-private-key-security.md`.
- Added a local development guide: `docs/local-sandbox-wallet-quickstart.md`.
- Updated `docs/security-guidelines.md` and `docs/integrator-notes.md` to reference the new policy.
- Modified parameter descriptions in all execution-capable `SKILL.md` files to deprecate `AGENT_PRIVATE_KEY` as the default, marking it specifically for "isolated test or compatibility setups" only.
- Splits execution policy between interactive agents and policy-bound autonomous agents.
- Differentiates asset-authorizing signers from shared gas sponsor or relayer wallets.
- Adds a concrete signer capability model for runtimes that do not want raw private-key access in skill prompts.

## Decisions

### 1. Conflict with Fully Autonomous Agents
The guidelines state: *"Require explicit human confirmation immediately before every signature."*
**Decision:** Use a separate category called **policy-bound autonomous agents** rather than weakening the default rule.

These agents may sign without per-transaction human approval only when chain, contract, method, and spend policy are enforced, the wallet is dedicated and low-value, and monitoring plus an emergency stop are in place.

### 2. Hardware Wallets for Server-Side Agents
The "Preferred Signer Order" lists "hardware wallet" as #1.
**Decision:** Split signer guidance by operating mode.

Hardware wallets remain first-class for interactive user-facing agents. HSM/KMS/MPC-backed remote signers are the preferred standard for automated server-side agents.

### 3. Quickstart & Local Dev UX Friction
By marking `AGENT_PRIVATE_KEY` as "unsafe-by-default", we create friction for new developers adopting the skills.
**Decision:** Add a dedicated local sandbox guide instead of softening the production policy.

The paved path is: disposable burner wallet, testnet by default, low balance, gitignored local `.env`, no chat paste, and immediate rotation on suspected exposure.

### 4. Gas Sponsorship vs. User Asset Signing
The guide says: *"Never reuse one signer across multiple users... Never use a treasury... wallet as an agent signer."*
**Decision:** Separate **asset-authorizing signers** from **gas sponsor or relayer signers**.

Asset-authorizing signers should not be shared across users. Shared sponsor wallets are acceptable only when they cannot approve, transfer, or custody user assets and are budget-capped, rate-limited, and policy-isolated.

### 5. Technical Standard for "Approved Signers"
The skills updated `AGENT_PRIVATE_KEY` to "an approved signer".
**Decision:** Define a portable signer capability model rather than one transport-specific variable.

The runtime should expose signer address, signer type, signer mode, approved chains, optional contract and method allowlists, whether human confirmation is required, and a policy or session identifier for auditability.

## Second Sweep Decisions

### 6. Enforcement Responsibility vs Skill Execution
If a skill can execute directly with `--execute`, relying on the skill to "pause" for human confirmation is weaker than runtime-level enforcement.
**Decision:** Prefer runtime-orchestrated confirmation and policy enforcement.

Skills should prefer returning unsigned transaction payloads, intent data, or execution plans. The runtime should own the final approval gate, signing step, and broadcast path.

### 7. Dynamic Spend Bounds for Autonomous Agents
Autonomous agents may need to size positions dynamically, which static signer policy alone cannot always express safely.
**Decision:** Use signer policy for where the agent may act, and contract-level controls for how much value it may move.

For dynamic sizing, prefer scoped allowances, vault-specific limits, strategy contracts, or guard contracts instead of trusting prompt-generated amounts or static signer policy alone.

### 8. Minor Type Clarity in Signer Interface
The signer interface should be explicit enough that different runtimes can implement the same contract consistently.
**Decision:** Clarify the expected shapes in the docs.

`approved_chains` should be documented as an array of chain IDs, and `approved_methods` should be documented as an optional array of strings containing method selectors or named actions.
