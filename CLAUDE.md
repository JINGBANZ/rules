# CLAUDE.md

<!--
  This is a generic, reusable template. Copy it to a project root and fill in the
  <angle-bracket> placeholders. Delete any section that doesn't apply.

  Guiding rule (from Anthropic's own docs): this file is loaded into EVERY session,
  so keep it short and high-signal. For each line ask:
  "Would removing this cause Claude to make a mistake?" If not, cut it.
  Target < 200 lines. A bloated file causes Claude to ignore the rules that matter.

  Include things Claude can't infer from the code. Do NOT restate standard language
  conventions, document the codebase file-by-file, or paste API docs (link instead).
-->

## Project overview

<!-- WHY this exists + WHAT it is, in 2-4 sentences. The mental model, not a feature list. -->
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

<!-- Only commands Claude can't guess from package.json/Makefile. Keep the canonical few. -->

| Task  | Command                       |
| ----- | ----------------------------- |
| Dev   | `<run locally>`               |
| Build | `<build>`                     |
| Test  | `<run the full suite>`        |
| Lint  | `<lint>`                      |
| Types | `<typecheck>`                 |
| Format| `<format>`                    |

## Working principles

<!--
  Stack-agnostic behavioral rules. Adapted from the widely-used "Karpathy guidelines"
  (community-distilled from Karpathy's notes on where coding agents fail). These target
  the common failure modes: silent wrong assumptions, over-engineering, scope creep.
-->

- **Think before coding.** State assumptions explicitly. If a request has multiple reasonable
  interpretations, surface them and ask — don't silently pick one. Push back when something
  looks wrong instead of running with it.
- **Simplicity first.** Write the minimum code that solves the problem. No speculative features,
  no abstractions for single-use code, no error handling for cases that can't occur. Ask whether
  a senior engineer would find the solution overcomplicated.
- **Surgical changes.** Every changed line should trace to the request. Don't refactor or "improve"
  nearby code, and don't remove pre-existing dead code, unless asked.
- **Goal-driven execution.** Turn the request into verifiable success criteria, state a brief plan
  for complex tasks, then loop until the criteria are met.

## Workflow

<!-- HOW Claude should work in THIS repo. Keep to the project-specific mechanics. -->

- **Explore → plan → implement.** For non-trivial changes, understand the relevant code and
  agree on an approach before editing. Skip planning only for small, well-scoped fixes.
- **Always verify before claiming done.** Run the relevant tests/build/lint and show the output.
  Don't assert success without evidence.
- **Match existing patterns.** Find a similar feature already in the codebase and follow its
  structure, naming, and conventions instead of introducing new ones.
- **Prefer running focused tests** over the whole suite while iterating, then run the full suite
  before finishing.

## Code style

<!--
  Let linters and formatters own mechanical style — don't restate rules a tool enforces.
  List ONLY project-specific conventions that differ from the ecosystem default.
-->

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

### Commit messages

Follow [Conventional Commits](https://www.conventionalcommits.org/): `type(scope): summary`

- **Format:** `<type>[optional scope]: <description>`, an optional body, then optional footers.
- **Types:** `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`, `chore`, `revert`.
- **Summary:** imperative mood ("add", not "added"/"adds"), lowercase, no trailing period, ≤ 72 chars.
- **Body** (when needed): explain *what* and *why*, not *how*; wrap at ~72 cols.
- **Breaking changes:** add `!` after the type/scope (`feat!:`) or a `BREAKING CHANGE:` footer.
- **One logical change per commit.** Don't bundle unrelated changes.

Example:

```
feat(auth): add token refresh on 401 responses

Sessions silently expired after 1h. Retry once with a refreshed
token before surfacing the error to the caller.

Closes #123
```

## Security & safety

- Never hardcode or log secrets, tokens, or credentials; read them from env/secret storage.
- Validate and sanitize all external input; treat user data as untrusted.
- <Domain-specific constraint, e.g. "Never run destructive DB migrations without confirmation.">

## Gotchas

<!-- Hard-won, non-obvious knowledge that has bitten people before. Highest value per line. -->

- <e.g. "The build caches aggressively — run `<clean cmd>` if you see stale output.">
- <e.g. "Module X looks unused but is loaded dynamically; don't delete it.">

## Further context (progressive disclosure)

<!--
  Keep this file lean. Put detailed, occasionally-needed docs in separate files and link
  them so Claude loads them only when relevant.
-->

- Architecture details: @docs/architecture.md
- Contribution guide: @CONTRIBUTING.md
- <Other: @docs/...>
