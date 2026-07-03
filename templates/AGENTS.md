# AGENTS.md

<!-- shared-rules:begin — machine-managed block; do not edit — propose changes in JINGBANZ/rules. -->
<!-- Empty until the first sync run populates it: Actions → "Sync shared rules" → Run workflow. -->
<!-- shared-rules:end -->

## Project overview

<2–4 sentences: what this project does, who it's for, the stack, and where to start reading. Ask
your coding agent to draft this section and the ones below from the codebase, then trim.>

## Setup

<Only the non-obvious steps: env vars, required tooling, quirks. Delete if the README covers it.>

## Commands

| Task     | Command                                            |
| -------- | -------------------------------------------------- |
| Dev      | `<run locally>`                                    |
| Build    | `<build — includes lint, typecheck, format check>` |
| Test     | `<run the full suite>`                             |
| **Gate** | `<one command: build + test, run before pushing>`  |

> **Gate** is the canonical pre-push check. Make it match CI so "passes locally" means "passes CI".

## Code style

<Only project conventions a linter doesn't already enforce, e.g. "Use the `Result` type; no throw.">

## Testing

<Framework, where tests live, expectations for new code, non-obvious setup.>

## Repository etiquette

- **Branches:** <naming convention, e.g. `feat/<short-desc>`, `fix/<short-desc>`>
- **Pull requests:** <what a PR needs, e.g. description of intent, tests passing, linked issue>

## Security & safety

<Domain-specific hard constraints, e.g. "Never run destructive DB migrations without confirmation."
The universal rules (secrets, input validation) are in the shared block above. Delete if none.>

## Gotchas

<Hard-won, non-obvious knowledge that has bitten people before. Highest value per line.>

## Further context

- **Design source of truth:** @wiki/index.md — specs, architecture, decisions, and current status.
