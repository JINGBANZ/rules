# AGENTS.md

<!-- shared-rules:begin — machine-managed block; the sync workflow overwrites everything inside. Propose changes in JINGBANZ/rules, not here. -->

> **Shared engineering rules** — maintained in [JINGBANZ/rules](https://github.com/JINGBANZ/rules)
> and synced automatically into the marker block of each consuming repo's `AGENTS.md`. Don't edit
> them in place — the next sync overwrites the block; propose changes in the rules repo instead.
> The **Gate** referenced below is the single build-and-test command each repo defines in its own
> Commands section.

## Working principles

- **Think before coding.** State assumptions explicitly. If a request has multiple reasonable
  interpretations, surface them and ask — don't silently pick one. Push back when something
  looks wrong instead of running with it.
- **Simplicity first.** Write the minimum code that solves the problem. No speculative features,
  no abstractions for single-use code, no error handling for cases that can't occur. Ask whether
  a senior engineer would find the solution overcomplicated.
- **Surgical changes.** Every changed line should trace to the request — but it's fine to refactor
  or improve nearby code and remove pre-existing dead code where there's clear room for improvement.
- **Goal-driven execution.** Turn the request into verifiable success criteria, state a brief plan
  for complex tasks, then loop until the criteria are met.
- **Document non-obvious decisions in comments** — explain *why*, not *what*.

## Workflow

- **Explore → plan → implement.** For non-trivial changes, understand the relevant code and
  agree on an approach before editing. Skip planning only for small, well-scoped fixes.
- **Evidence, not assertion.** Before claiming work is done, run the **Gate** command and show the
  output. Don't say "it works" without the passing result to back it.
- **Match existing patterns.** Before adding a file, find where similar code already lives and mirror
  its structure, naming, and idioms. Don't impose a pattern the repo doesn't already use.
- **Prefer running focused tests** over the whole suite while iterating, then run the full **Gate**
  before finishing.
- **Open a PR when the work is done.** Once the change is complete and the **Gate** passes, commit
  to a branch and open a pull request without waiting to be asked. Don't leave finished work
  uncommitted on a local branch.

## Commit messages

Follow [Conventional Commits](https://www.conventionalcommits.org/): `type(scope): summary` in
lowercase imperative mood (`feat`, `fix`, `docs`, `refactor`, `perf`, `test`, `build`, `ci`, `chore`).
Mark breaking changes with `!` (`feat!:`) or a `BREAKING CHANGE:` footer. One logical change per commit.

## Security & safety

- Never hardcode or log secrets, tokens, or credentials; read them from env/secret storage.
- Validate and sanitize all external input; treat user data as untrusted.

## Never

- **Never** skip pre-commit/pre-push hooks (e.g. `--no-verify`) or the **Gate**.
- **Never** commit secrets, `.env` files, or credentials.
- **Never** commit directly to the default branch; open a PR.
- **Never** `git push --force` to a shared branch.
- **Never** modify or delete a test to make a broken change pass — fix the code, not the test.
- **Never** suppress an error or warning to hide a problem — address the root cause.

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

## Gotchas

<Hard-won, non-obvious knowledge that has bitten people before. Highest value per line.>

## Further context

- **Design source of truth:** @wiki/index.md — specs, architecture, decisions, and current status.
