# Generic Local Installation

Use this option for agent runtimes that can read skills from a filesystem path but do not have a dedicated install guide here yet.

## Supported Patterns

- copy one or more skill folders into a runtime-managed skills directory
- symlink skill folders into a runtime-managed skills directory
- vendor this repository as a git submodule and point the runtime at `skills/`

## Minimal Runtime Assumption

The runtime should:

- discover skills from a folder path
- treat `SKILL.md` as the entry document
- ignore missing optional support folders

## Recommended Validation

- install one skill first
- test with both trigger examples from the skill
- confirm any referenced scripts or docs are reachable from the cloned repository
