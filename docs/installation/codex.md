# Install For Codex-Compatible Runtimes

Use this option when your runtime discovers local skills from a skills directory and treats `SKILL.md` as the entrypoint.

## Local Copy

1. Clone this repository.
2. Copy one or more folders from `skills/` into your local skills directory.
3. Restart or reload the runtime so it re-indexes skills.
4. Verify discovery with a prompt that should trigger the installed skill.

## Symlink

If your runtime supports symlinks, link a skill folder directly into the runtime skills path so updates in this repo are immediately visible after reload.

## Verification

Check that:

- the runtime sees the skill by its declared `name`
- the `description` is visible in discovery tooling if supported
- trigger prompts produce behavior consistent with the skill instructions
