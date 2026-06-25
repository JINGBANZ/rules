# wiki/CLAUDE.md

Conventions for maintaining this wiki. Read before editing any file under `wiki/`.

The root `CLAUDE.md` describes *how to work in this repo*; this file describes *how to keep the
project's documentation healthy as it grows*. The wiki is the project's design source of truth — the
durable answer to "what is this system, how does it work, and why is it built this way" — written so a
fresh agent or human can pick the project up mid-stream without re-deriving anything. For the current
list of pages, see [`index.md`](./index.md); for a fresh start, see [`status.md`](./status.md).

## Principles

The wiki is a **living knowledge base** — the project's current model of itself — not a historical
archive. Two failure modes to design against:

- **Fossilization.** Dated filenames, `v1`/`v2` copies, append-only specs, prose that narrates what
  changed. Git already stores history; the wiki stores *what is true now*.
- **Fragmentation.** Ten micro-pages that drift apart. One well-organized page beats a directory of
  stubs.

Every convention below serves those two principles, and rests on a few industry standards: docs-as-code
(Markdown in the repo, changed via PR, reviewed like code), single-source-of-truth, present-tense
reference docs, and context engineering for agents. See [Further reading](#further-reading).

## Conventions

### 1. Edit in place. Never append dated or versioned copies.

If the design changes, update the existing page directly. Never create `architecture-2026-05.md` or
`spec-v2.md`. The git log is the history; the wiki is the present.

### 2. Read the full page before editing it.

Wiki pages are dense and cross-referential. A local edit that contradicts a distant section is the most
common failure mode. Non-negotiable for any substantive edit.

### 3. Write in the present. Don't narrate refactors.

The wiki describes the system *as it is now*, not how it got here. After a change, a page should read as
if the removed concept never existed — a fresh reader cannot tell whether something was removed
yesterday or never existed. Delete these on sight:

- "X was removed / replaced / deprecated."
- "The original design had Y; we now do Z."
- "Previously…", "No longer…", "Used to be…"
- `~~struck-through~~` list items, and obsolete non-goals whose removal would be the actual news.

History belongs in the git log and in decision records. A decision record is the **one** place where
"what changed and why" is the point (see [Convention 8](#8-log-load-bearing-decisions-in-one-place-dont-accumulate-decision-files)).

### 4. Reference source; don't paste code.

The wiki holds the *why* and the *shape* (rationale, tradeoffs, how the pieces fit). The *what* —
schemas, signatures, config values, exact constants — lives in the code. Point to it by path and symbol
(`auth lives in src/auth/`, `see Config in src/config.ts`) instead of copying it in. A pasted snippet
drifts the moment the code changes; a pointer survives. Same rule for facts that live in config
(`package.json`, `pyproject.toml`, etc.): link, don't restate.

For diagrams, prefer text that diffs and renders in-repo (Mermaid in Markdown, or C4/PlantUML) over a
committed image binary — a PNG fossilizes exactly the way
[Convention 1](#1-edit-in-place-never-append-dated-or-versioned-copies) warns against.

### 5. Cross-reference; don't duplicate.

Document each fact in exactly one page. If another page needs it, **link** to it. Duplication causes
drift: when one copy updates and the other doesn't, the wiki lies.

### 6. Don't create empty pages.

If you can't write at least three meaningful sentences about a topic, don't create a page for it. A
stub with a `TODO` is worse than a missing page: the stub gets indexed and read, while a missing page
prompts you to add the content where it belongs.

The structural scaffold pages — `index.md`, `status.md`, `decisions.md` — are the deliberate exception:
they exist for structure, not content, so they're present from the start even when nearly empty. An
empty `decisions.md` is fine until the first decision; a `status.md` still full of `<placeholders>`
after work has started is the stub this rule forbids.

### 7. Default to extending a page. Promote to a new page only when earned.

Pre-decomposition is the failure mode — creating `worker.md`, `safety-model.md`, `wiki/agents/` before
any of them has enough content to stand alone. Fragmentation is harder to unwind than consolidation.

Start as a section inside an existing page. Promote it to its own page **only when at least one** holds:

- **It's drowning its host.** The section has grown past ~20% of the parent page, or far deeper than
  the sections around it.
- **Three or more places link into it.** It has earned a stable link target.
- **A genuinely new concept arrives** — a new subsystem, a new top-level surface, a new role. Not a
  refinement of something that already exists.

**Concrete-noun test:** can you finish "X is a ___" with a non-generic answer? "The sandbox is a
worktree-isolated process boundary" → page-worthy; "the eval harness is the offline scorer for model
outputs" → page-worthy; "good error handling matters" → not.

**Cold start:** this rule governs *splitting*, not whether the first page may exist. On a brand-new
wiki, create the first core page (usually `architecture.md`) as soon as you can write three meaningful
sentences about the system ([Convention 6](#6-dont-create-empty-pages)) — don't wait for a promotion
trigger.

The wiki stays flat (`wiki/*.md`) until flat stops working. Don't pre-create directories; taxonomy
emerges from the material. **Whenever you add, rename, or remove a page, update
[`index.md`](./index.md) in the same edit** — a page not in the index is invisible.

### 8. Log load-bearing decisions in one place. Don't accumulate decision files.

Decisions live as a running log on a single page, [`decisions.md`](./decisions.md): one entry per
choice — what you chose, why, and the alternative you rejected. That's the whole mechanism — no numbered
files, no per-decision index, no superseding ceremony. A growing folder of decision records is its own
maintenance burden and a fresh source of drift (more files to sync, more cross-links to rot); resist it.

It's a dedicated page, **not** a section of [`status.md`](./status.md), because the two have opposite
lifecycles: `status.md` is volatile current-state that gets rewritten as work lands, while the decision
log is durable and append-mostly. Mixing them bloats the page a fresh agent reads first and risks
overwriting rationale you meant to keep.

Only log a decision that's genuinely **load-bearing and non-obvious** — one a future reader would
otherwise re-litigate, or a dead end they'd re-walk. Skip anything the code, commit, or PR already
explains. If a choice doesn't earn its entry, leave it out.

**The split holds:** core pages describe *what the system is and how it works*; the decision log holds
only *why this over the alternatives*. The log is the **one** place past rationale is allowed to
persist — [Convention 3](#3-write-in-the-present-dont-narrate-refactors)'s "delete the narration" rule
does **not** apply to it, precisely because the rejected alternative is the thing you don't want to
lose.

**Supersede; don't rewrite.** Date each entry so "newest last" is verifiable. When a later decision
reverses an earlier one, leave the original in place and append `**Superseded by:** <new entry>` to it
(and `**Supersedes:** <old entry>` on the new one). This is the backward-looking complement to each
entry's `Revisit if:` line — it recovers the one thing a numbered ADR would carry (decision lifecycle),
in a line rather than a folder, and it's what keeps a reversed decision from silently contradicting the
present-tense core pages.

## Keep-in-sync checklist

Run this whenever you land a change that alters behavior, structure, or a documented fact — in the
**same change (PR or commit)** as the code, not as a later cleanup. Doc updates ride with the change
that caused them; a change that alters documented behavior without a doc update is incomplete. Stale
docs erode trust faster than they can rebuild it — every agent who acts on a fossilized claim makes the
next one trust the wiki less.

1. **Update [`status.md`](./status.md).** Move newly-built things from "Not yet built" to "Built" with
   a file pointer. If a "Not yet built" item was abandoned rather than built, delete it (and log why in
   [`decisions.md`](./decisions.md) if the call was load-bearing). Update the current phase and the next
   action. This is the page a fresh agent reads first.
2. **Create or update the relevant core page(s)** in place ([Convention 1](#1-edit-in-place-never-append-dated-or-versioned-copies)),
   in present tense ([Convention 3](#3-write-in-the-present-dont-narrate-refactors)).
3. **Log any load-bearing decision** in [`decisions.md`](./decisions.md) ([Convention 8](#8-log-load-bearing-decisions-in-one-place-dont-accumulate-decision-files)).
4. **If a page was added, renamed, or removed, update [`index.md`](./index.md).**
5. **Resolve cross-reference drift.** Confirm each link in the touched pages still resolves, and that
   no file pointer points at a path that no longer exists.
6. **Sweep for duplicated facts.** Anywhere the same fact lives in two places — code ↔ wiki, page ↔
   page, code ↔ config — one can rot independently. Collapse to a single source or link. If you find a
   divergence you can't fix now, note it under "Known drift" in [`status.md`](./status.md) — never
   leave two facts silently disagreeing.

A change isn't done until this checklist is. If a fresh agent couldn't reconstruct what's built, what
isn't, and where to start next from `index.md` + `status.md` + the touched core pages, it's unfinished —
regardless of whether the code merged.

## What does *not* live in the wiki

- **Conversation notes, brainstorms, plans, exploratory drafts.** These are scratch. If a brainstorm
  produces a real decision, extract the decision ([Convention 8](#8-log-load-bearing-decisions-in-one-place-dont-accumulate-decision-files))
  and discard the brainstorm — don't check it in.
- **Tutorials, quickstarts, user guides.** Those serve external users and belong in `README.md`. The
  wiki is the project's internal model of itself.
- **Generated artifacts, logs, runtime output.** Not documentation.
- **Anything the code already states** ([Convention 4](#4-reference-source-dont-paste-code)).

## Conciseness

Documentation is context. A bloated page costs tokens on every read and buries the signal. Prefer the
smallest set of high-signal words, one canonical example over an exhaustive list of edge cases, and a
link over a restated paragraph. Keep `index.md` a lightweight map, not a second copy of the content.

## Cross-tool compatibility

This file is named `CLAUDE.md` because Claude Code reads that name; other agents (Cursor, Codex,
Copilot, Gemini CLI) read the vendor-neutral `AGENTS.md`. Keep a single source of truth:
[`AGENTS.md`](./AGENTS.md) is a symlink to this file. Edit only `CLAUDE.md`; never let the two diverge.

## If you get stuck

If a wiki page says one thing and the code says another, and no decision record explains the gap, that's
a drift signal. Treat the code as the ground truth for *what*, but flag the contradiction to a human
rather than silently rewriting the page to match — the page may encode intent the code has drifted from.

## Further reading

The conventions above align with these standards; consult them when a situation isn't covered here.

- **AGENTS.md** — the open, cross-tool agent-instructions standard: <https://agents.md/>
- **llms.txt** — a curated navigation map for agents; `index.md` plays this role *inside* the repo,
  while a published `llms.txt` targets an external docs site (out of scope for an in-repo wiki):
  <https://llmstxt.org/>
- **Architecture Decision Records** — the standard heavier form, only if a project ever genuinely
  outgrows the decision log: <https://adr.github.io/>
- **Diátaxis** — a vocabulary for doc types (tutorial / how-to / reference / explanation):
  <https://diataxis.fr/>
- **Docs-as-code** — Markdown in the repo, reviewed via PR: <https://www.writethedocs.org/guide/docs-as-code/>
- **Effective context engineering for agents** — why an entry point, file pointers, and conciseness
  matter: <https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents>
