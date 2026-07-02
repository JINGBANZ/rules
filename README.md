# rules

Shared engineering rules for coding agents — one `AGENTS.md` per repo that Claude Code, Codex, and
any AGENTS.md-compatible tool all read, with the universal half kept identical across repos by an
automated sync.

Agent instructions mix two kinds of content with different owners:

- **Universal rules** ([`shared-rules.md`](./shared-rules.md)) — working principles, workflow,
  commit format, security basics, the hard "never"s. Identical in every repo; machine-synced.
- **Project-specific context** — stack, commands, testing, gotchas. Authored per repo (mostly by the
  agent itself, from the codebase); never touched by the sync.

## How it works

Each consuming repo carries three small files:

| File | Role |
| --- | --- |
| `AGENTS.md` | The single instruction file. Starts with a machine-managed marker block holding the shared rules; project-specific sections follow. |
| `CLAUDE.md` | One line — `@AGENTS.md` — so Claude Code loads the same file (it doesn't read `AGENTS.md` natively). |
| `.github/workflows/sync-shared-rules.yml` | A ~15-line stub calling the reusable sync workflow in [JINGBANZ/workflows](https://github.com/JINGBANZ/workflows) weekly, and on demand. Copied from that repo's `templates/`. |

The reusable workflow fetches `shared-rules.md` from this repo (it's public — no token needed),
splices it between the `<!-- shared-rules:begin -->` / `<!-- shared-rules:end -->` markers in
`AGENTS.md`, and opens a pull request in the consuming repo if anything changed. It runs on the
consuming repo's own `GITHUB_TOKEN` — there is no PAT to create, rotate, or grant per repo, and no
central credential with write access to everything. It also self-heals: if someone edits inside the
marker block, the next run opens a correcting PR.

## Adopting the rules in a repo

1. Copy [`templates/AGENTS.md`](./templates/AGENTS.md) and
   [`templates/CLAUDE.md`](./templates/CLAUDE.md) to the project root.
2. Copy the sync stub from
   [`JINGBANZ/workflows/templates/sync-shared-rules.yml`](https://github.com/JINGBANZ/workflows/blob/main/templates/sync-shared-rules.yml)
   to `.github/workflows/`.
3. In the repo settings, enable **Settings → Actions → General → Allow GitHub Actions to create and
   approve pull requests** (the sync needs it to open PRs).
4. Run the sync once (**Actions → Sync shared rules → Run workflow**) and merge the PR it opens —
   this populates the empty marker block in `AGENTS.md` with the current shared rules.
5. Ask your coding agent to fill in the sections below the marker block in `AGENTS.md` from the
   codebase; delete any section that doesn't apply. Don't touch the marker block itself.
6. (Optional) Copy [`templates/wiki/`](./templates/wiki) as the design-docs starter (see
   [The `wiki/` template](#the-wiki-template)).

## Changing the shared rules

Edit [`shared-rules.md`](./shared-rules.md) in a PR here. Once merged, every consuming repo receives
the change as an automated PR at its next scheduled sync — or immediately via the sync workflow's
**Run workflow** button in that repo.

Add a rule only when an agent makes a mistake the rule would have prevented; prune rules that no
longer earn their place. Every line loads into every session of every consuming repo.

## Where the sync machinery lives

All workflow files live in [JINGBANZ/workflows](https://github.com/JINGBANZ/workflows), following
that repo's convention (reusable workflows in `.github/workflows/`, caller stubs in `templates/`):

- `.github/workflows/sync-shared-rules.yml` — the reusable workflow: fetches `shared-rules.md`,
  splices the marker block, opens the PR.
- `templates/sync-shared-rules.yml` — the stub each consuming repo copies.

This repo holds only the synced content and the seed files — no workflows. The seed's marker block
ships empty; the first sync run in each consuming repo fills it, so the rules exist in exactly one
place here.

### Known limitations

- **Propagation is scheduled (weekly), not instant.** Trigger a repo's stub manually when you don't
  want to wait. Instant push fan-out would require a cross-repo credential (PAT or GitHub App) held
  centrally — deliberately avoided.
- **CI doesn't run on sync PRs.** PRs opened with `GITHUB_TOKEN` don't trigger other workflows
  (GitHub's recursion guard). For a rules-only change that's fine; close and reopen the PR if you
  need CI to run on it.

## Cross-tool compatibility

`AGENTS.md` is the vendor-neutral standard read natively by Codex, Cursor, Copilot, Gemini CLI, and
others. Claude Code reads `CLAUDE.md`, so each repo carries the one-line `@AGENTS.md` shim — Claude
Code expands the import at load time, and both tools see identical instructions. A symlink also works
on macOS/Linux, but the one-line file behaves everywhere, including Windows checkouts.

The shared rules are spliced **inline** into `AGENTS.md` rather than kept as a separate imported
file, because only Claude Code has an import syntax — Codex and other AGENTS.md readers only see
content that is physically in the file.

## Authoring guidance

Core principle: **`AGENTS.md` loads into every session, so keep it lean.** For each line, ask
*"would removing this cause the agent to make a mistake?"* If not, cut it. Don't restate anything the
agent can infer from the code, standard language conventions, or full API docs (link to those
instead).

Section-by-section notes for the seed:

- **Shared rules block** — machine-managed; never hand-edit it in a consuming repo. Propose changes
  in this repo instead.
- **Project overview** — The WHY and WHAT in 2–4 sentences: the mental model, not a feature list.
- **Setup** — Only the steps that aren't obvious from the repo (env vars, required tooling, quirks).
- **Commands** — Only the canonical few an agent can't guess. Lint, typecheck, and format checks
  belong *inside* Build. The **Gate** is the single command to run before claiming work is done or
  pushing; make it match CI so "passes locally" means "passes CI".
- **Code style** — Let linters and formatters own mechanical style; don't restate rules a tool
  enforces. List only project-specific conventions that differ from the ecosystem default.
- **Testing** — Framework, where tests live, expectations, and any non-obvious setup gotchas.
- **Repository etiquette** — Branch and PR conventions (the commit-message format is already in the
  shared rules).
- **Gotchas** — Hard-won, non-obvious knowledge that has bitten people before. Highest value per line.
- **Further context** — `AGENTS.md` is *how to work*; the *what we're building* (design, specs,
  decisions, status) lives in the wiki. Link out so the agent loads detail only when needed. Nested
  instruction files in subdirectories are pulled in when the agent works in those areas.

## The `wiki/` template

`AGENTS.md` is *how to work in the repo*; the **wiki** is *what we're building* — the project's design
source of truth (architecture, decisions, current status), written so a fresh agent or human can pick
the project up mid-stream. [`templates/wiki/`](./templates/wiki) is a reusable starting point you can
copy into any project, and `wiki/AGENTS.md` is the rule that tells coding agents how to keep it
healthy as it grows.

How to use it:

1. Copy the `templates/wiki/` folder to `wiki/` at the root of your project.
2. Fill in `wiki/index.md` and `wiki/status.md` — replace the `<angle-bracket>` placeholders. Leave
   `wiki/AGENTS.md` and `wiki/CLAUDE.md` as-is; they're generic rules, not fill-in templates.
3. Add core pages (`architecture.md`, etc.) only as they earn their place — don't pre-create stubs.
4. Decisions: log load-bearing choices (what + why, including the rejected alternative) in
   `wiki/decisions.md` — a single running log. There's no ADR folder by design; a growing set of
   numbered decision files is a maintenance burden and a drift source, so keep it to one lean page.
5. The seed `AGENTS.md` already points at the wiki under **Further context** (`@wiki/index.md`) so
   agents load design detail only when they need it.

`wiki/AGENTS.md` is a nested instruction file: agents load it when working under `wiki/`. It distills
industry standards — docs-as-code, single-source-of-truth, present-tense reference docs, a single lean
decision log (no ADR sprawl), and context engineering for agents — into conventions that fight the two
ways a knowledge base rots: **fossilization** (dated copies, change-narration) and **fragmentation**
(drifting stubs).

Sources for those standards (kept here, not in the agent-loaded rule):
[AGENTS.md](https://agents.md/),
[docs-as-code](https://www.writethedocs.org/guide/docs-as-code/),
[Diátaxis](https://diataxis.fr/),
[ADRs](https://adr.github.io/),
[llms.txt](https://llmstxt.org/), and
[effective context engineering for agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents).

## What's in this repo

| File | Purpose |
| --- | --- |
| `shared-rules.md` | The universal rules — single source of truth, synced into consuming repos. |
| `templates/AGENTS.md` | The seed consumers copy: empty shared-rules marker block + lean project sections. |
| `templates/CLAUDE.md` | The one-line `@AGENTS.md` shim for Claude Code. |
| `templates/wiki/` | The design-docs starter: maintenance rules, index, status, and decision log. |
| `AGENTS.md`, `CLAUDE.md` | Instructions for agents working on **this** repo (not part of the template). |
| `README.md` | This file — how the sync works, adoption steps, and authoring guidance. |

The sync workflow and its per-repo stub live in
[JINGBANZ/workflows](https://github.com/JINGBANZ/workflows), not here.
