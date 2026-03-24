# Install For OpenClaw

Use this option when OpenClaw is configured to load skills from local folders.

## Option A: Local Clone

1. Clone this repository locally.
2. Copy or symlink desired skill folders from `skills/` into your OpenClaw skills path.
3. Restart or reload OpenClaw.
4. Verify by sending a prompt that should trigger the skill.

## Option B: Git Submodule

Add this repository as a submodule inside the workspace that OpenClaw scans for skills, then point OpenClaw at the checked-out `skills/` directory.

## Expected Contract

Each skill exposes:

- `SKILL.md` as the entrypoint
- optional `references/`, `scripts/`, `assets/`, and `examples/` folders

OpenClaw users should not rely on optional folders for skill discovery.
