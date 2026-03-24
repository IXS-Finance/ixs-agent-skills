# Adding A Skill

Use this flow when publishing a new skill to this repository.

## 1. Create The Folder

Create `skills/<skill-slug>/` using lowercase hyphen-case.

Optional support folders are allowed:

- `references/`
- `scripts/`
- `assets/`
- `examples/`

## 2. Write `SKILL.md`

Every skill must include:

- YAML frontmatter with required metadata:
  - `name`
  - `description`
  - `owner`
  - `status`
  - `version`
- a short "When to use" section
- at least 2 trigger examples
- a workflow or instruction section
- a constraints section

Keep `SKILL.md` concise. Move long reference material into `references/`.

## 3. Document Cross-Repo Assumptions

If the skill depends on `ixs-api`, `ixs-ui`, `ixs/interface`, or another repository:

- state that dependency clearly in metadata or body text
- describe the expected source of truth
- call out any required docs, commands, or paths

## 4. Update Discovery Docs

For every new skill:

- add it to the README skill table
- add supporting docs if it introduces a new runtime or install pattern
- update governance or authoring guidance if the shared contract changes

## 5. Validate Before PR

Ensure:

- the slug matches the folder name
- the declared skill `name` is unique in the repo
- status is one of `draft`, `active`, `deprecated`
- trigger examples are concrete enough for a human reviewer to test

## 6. Submit A PR

Open a PR with:

- the new skill folder
- README updates
- any required documentation changes
