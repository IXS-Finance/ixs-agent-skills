# Governance

This repository uses moderate governance.

## Contribution Rules

- all changes land through pull requests
- every new skill must include an owner, status, version, and at least 2 trigger examples
- every new skill must document any cross-repo dependency or assumption
- changes to shared conventions must update README or docs in the same PR

## Review Expectations

Reviewers should verify:

- the skill is clearly scoped
- the trigger examples are specific
- the workflow is actionable
- the skill does not hide critical assumptions in external references
- any IXS-private dependencies are named clearly

## Status Policy

- `draft` for work in progress or internal trial use
- `active` for published skills expected to work as documented
- `deprecated` for skills kept only for compatibility or migration guidance

## Versioning Policy

- version skills individually in `SKILL.md`
- increase the version when behavior or instructions change materially
- treat metadata-only or typo-only edits as patch-level changes if versioning is updated

## CI Policy

Pull requests should pass structural validation before merge. CI checks:

- folder naming
- required `SKILL.md`
- metadata presence
- duplicate skill names
- expected installation docs
