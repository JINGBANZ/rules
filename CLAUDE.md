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

## Shared rules

Universal rules, shared verbatim across all repos and synced automatically — they apply in full here.
The sections after this one add project-specific context on top.

@shared-rules.md

## Code style

- Style and formatting are enforced by `<linter/formatter>`; run it before finishing.
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

## Security & safety

- <Domain-specific constraint, e.g. "Never run destructive DB migrations without confirmation.">

## Gotchas

- <e.g. "The build caches aggressively — run `<clean cmd>` if you see stale output.">
- <e.g. "Module X looks unused but is loaded dynamically; don't delete it.">

## Further context

- **Design source of truth:** @wiki/index.md — specs, architecture, decisions, and current status.
- Contribution guide: @CONTRIBUTING.md (if present)
