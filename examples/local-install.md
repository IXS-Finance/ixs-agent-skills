# Local Install Example

Example workflow for any runtime that loads skills from a local directory:

1. Clone `ixs-agent-skills`.
2. Copy `skills/inspect-vault/` into the runtime skills folder.
3. Reload the runtime.
4. Set `IXS_API_BASE_URL`, `IXS_VAULT_ID`, and the wallet credential source expected by your runtime.
5. Ask the runtime to inspect the vault and summarize the wallet position.
6. Confirm the runtime follows the workflow in `SKILL.md`.
