# rules

A generic, reusable [`CLAUDE.md`](./CLAUDE.md) template for coding-agent instructions, plus an
[`AGENTS.md`](./AGENTS.md) symlink for cross-tool compatibility.

[`CLAUDE.md`](./CLAUDE.md) is kept comment-free so every line is a rule the agent reads. This README
holds the guidance for *authoring and filling in* the template — the part that shouldn't live in the
file that loads into every session.

## How to use it

1. Copy `CLAUDE.md` to the root of your project.
2. Replace every `<angle-bracket>` placeholder with the real value for that project.
3. Delete any section that doesn't apply — there are no required sections.
4. (Optional) Add the `AGENTS.md` symlink so non-Claude agents read the same file (see below).
5. Keep it lean over time. Add a rule only when an agent makes a mistake that rule would have
   prevented; prune rules that no longer earn their place.

### Cross-tool compatibility (`AGENTS.md`)

`AGENTS.md` is the vendor-neutral open standard read by Cursor, Codex, Copilot, Gemini CLI, and
others; Claude Code reads `CLAUDE.md`. To serve both from a single source of truth, this repo makes
`AGENTS.md` a symlink to `CLAUDE.md`:

```bash
ln -s CLAUDE.md AGENTS.md
```

Git stores it as a real symlink (mode `120000`), so it clones correctly on macOS/Linux. On Windows,
symlinks need `core.symlinks=true`; if you have Windows collaborators, instead make `CLAUDE.md` a
one-line file containing `@AGENTS.md` (Claude Code's import syntax) and keep `AGENTS.md` as the real
file.

## Authoring guidance

Core principle: **`CLAUDE.md` loads into every session, so keep it lean.** For each line, ask
*"would removing this cause the agent to make a mistake?"* If not, cut it. Don't restate anything the
agent can infer from the code, standard language conventions, or full API docs (link to those
instead).

Section-by-section notes for filling in the template:

- **Project overview** — The WHY and WHAT in 2–4 sentences: the mental model, not a feature list.
- **Setup** — Only the steps that aren't obvious from the repo (env vars, required tooling, quirks).
- **Commands** — Only the canonical few an agent can't guess. Lint, typecheck, and format checks
  belong *inside* Build. The **Gate** is the single command to run before claiming work is done or
  pushing; make it match CI so "passes locally" means "passes CI".
- **Working principles** — Stack-agnostic behavioral rules (the community "Karpathy guidelines"),
  targeting the common agent failure modes: silent wrong assumptions, over-engineering, scope creep.
  These are universal — keep them as-is across projects.
- **Workflow** — How an agent should work in *this* repo; the project-specific mechanics.
- **Code style** — Let linters and formatters own mechanical style; don't restate rules a tool
  enforces. List only project-specific conventions that differ from the ecosystem default.
- **Testing** — Framework, where tests live, expectations, and any non-obvious setup gotchas.
- **Repository etiquette** — Branch and PR conventions, and commit-message format (Conventional
  Commits).
- **Security & safety** — Secrets handling, input validation, and domain-specific hard constraints.
- **Never** — Hard prohibitions. Short, absolute, and enforced — the lines worth being blunt about.
- **Gotchas** — Hard-won, non-obvious knowledge that has bitten people before. Highest value per line.
- **Further context** — `CLAUDE.md` is *how to work*; the *what we're building* (design, specs, ADRs,
  status) lives in the wiki. Link out so the agent loads detail only when needed. Nested `CLAUDE.md`
  files in subdirectories are pulled in on demand when the agent works in those areas.

## The `wiki/` template

`CLAUDE.md` is *how to work in the repo*; the **wiki** is *what we're building* — the project's design
source of truth (architecture, decisions, current status), written so a fresh agent or human can pick
the project up mid-stream. The `wiki/` folder here is a reusable starting point you can copy into any
project, and `wiki/CLAUDE.md` is the rule that tells coding agents how to keep it healthy as it grows.

How to use it:

1. Copy the `wiki/` folder to the root of your project.
2. Fill in `wiki/index.md` and `wiki/status.md` — replace the `<angle-bracket>` placeholders. Leave
   `wiki/CLAUDE.md` as-is; it's a generic rule, not a fill-in template.
3. Add core pages (`architecture.md`, etc.) only as they earn their place — don't pre-create stubs.
4. Decisions: log load-bearing choices (what + why, including the rejected alternative) in
   `wiki/decisions.md` — a single running log. There's no ADR folder by design; a growing set of
   numbered decision files is a maintenance burden and a drift source, so keep it to one lean page.
5. Point the root `CLAUDE.md` at the wiki under **Further context** (e.g. `@wiki/index.md`) so agents
   load design detail only when they need it.

`wiki/CLAUDE.md` is a nested instruction file: Claude Code loads it on demand when an agent works under
`wiki/`. It distills industry standards — docs-as-code, single-source-of-truth, present-tense reference
docs, a single lean decision log (no ADR sprawl), and context engineering for agents — into conventions
that fight the two ways a knowledge base rots: **fossilization** (dated copies, change-narration) and
**fragmentation** (drifting stubs). See the "Further reading" links at the bottom of that file for the
sources.

## What's in this repo

| File | Purpose |
| --- | --- |
| `CLAUDE.md` | The template — comment-free, placeholder-driven agent instructions. |
| `AGENTS.md` | Symlink to `CLAUDE.md` for AGENTS.md-compatible agents. |
| `README.md` | This file — how to use and fill in the template. |
| `wiki/CLAUDE.md` | Generic rule for maintaining a project wiki (the reusable deliverable). |
| `wiki/AGENTS.md` | Symlink to `wiki/CLAUDE.md` for cross-tool agents. |
| `wiki/index.md`, `wiki/status.md` | Navigation index and mid-stream handoff page (fill-in templates). |
| `wiki/decisions.md` | The decision log — load-bearing choices and their rationale (one page, no ADR folder). |
