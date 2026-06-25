# CLAUDE.md

## Project overview

<One-paragraph description: what this project does, who it's for, and the core problem it solves.>

- **Stack:** <languages, frameworks, runtime, package manager, e.g. TypeScript / Node 20 / pnpm>
- **Architecture:** <e.g. monorepo with apps/* and packages/*; or single service; key boundaries>
- **Entry points:** <where to start reading, e.g. `src/main.ts`, `cmd/server/`>

## Setup

```bash
<install dependencies, e.g. pnpm install>
<bootstrap env / secrets, e.g. cp .env.example .env>
```

- **Required env vars / tooling quirks:** <anything non-obvious that must be set or installed first>

## Commands

| Task     | Command                                              |
| -------- | ---------------------------------------------------- |
| Dev      | `<run locally>`                                      |
| Build    | `<build — includes lint, typecheck, format check>`  |
| Test     | `<run the full suite>`                               |
| **Gate** | `<one command: build + test, run before pushing>`    |

> **Gate** is the canonical pre-push check. Make it match CI so "passes locally" means "passes CI".

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

## Workflow

- **Explore → plan → implement.** For non-trivial changes, understand the relevant code and
  agree on an approach before editing. Skip planning only for small, well-scoped fixes.
- **Evidence, not assertion.** Before claiming work is done, run the **Gate** command and show the
  output. Don't say "it works" without the passing result to back it.
- **Match existing patterns.** Before adding a file, find where similar code already lives and mirror
  its structure, naming, and idioms. Don't impose a pattern the repo doesn't already use.
- **Prefer running focused tests** over the whole suite while iterating, then run the full **Gate**
  before finishing.

## Code style

- Style and formatting are enforced by `<linter/formatter>`; run it before finishing.
- Document non-obvious decisions in comments — explain *why*, not *what*.
- <Project-specific convention 1, e.g. "Use the `Result` type for fallible functions; no throw.">
- <Project-specific convention 2, e.g. "Import from package roots, never deep paths.">

## Testing

- **Framework:** <e.g. Vitest / pytest / go test>
- **Where tests live:** <e.g. co-located `*.test.ts`, or `tests/`>
- **Expectations:** <e.g. add/extend tests for behavior changes; keep coverage on critical paths>
- <Any gotchas, e.g. "Integration tests need a local DB: `<command>`.">

## Repository etiquette

- **Branches:** <naming convention, e.g. `feat/<short-desc>`, `fix/<short-desc>`>
- **Pull requests:** <what a PR needs, e.g. description of intent, tests passing, linked issue>
- **Never commit:** secrets, generated artifacts, or large binaries.

### Commit messages

Follow [Conventional Commits](https://www.conventionalcommits.org/): `type(scope): summary` in
lowercase imperative mood (`feat`, `fix`, `docs`, `refactor`, `perf`, `test`, `build`, `ci`, `chore`).
Mark breaking changes with `!` (`feat!:`) or a `BREAKING CHANGE:` footer. One logical change per commit.

## Security & safety

- Never hardcode or log secrets, tokens, or credentials; read them from env/secret storage.
- Validate and sanitize all external input; treat user data as untrusted.
- <Domain-specific constraint, e.g. "Never run destructive DB migrations without confirmation.">

## Never

- **Never** skip pre-commit/pre-push hooks (e.g. `--no-verify`) or the **Gate**.
- **Never** commit secrets, `.env` files, or credentials.
- **Never** commit directly to the default branch; open a PR.
- **Never** `git push --force` to a shared branch.
- **Never** modify or delete a test to make a broken change pass — fix the code, not the test.
- **Never** suppress an error or warning to hide a problem — address the root cause.

## Gotchas

- <e.g. "The build caches aggressively — run `<clean cmd>` if you see stale output.">
- <e.g. "Module X looks unused but is loaded dynamically; don't delete it.">

## Further context

- **Design source of truth:** @wiki/index.md — specs, architecture, decisions, and current status.
- Contribution guide: @CONTRIBUTING.md (if present)
