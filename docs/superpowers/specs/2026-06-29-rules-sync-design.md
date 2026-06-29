# Design: propagate shared rules to consuming repos

**Date:** 2026-06-29
**Status:** Proposed (this PR)

## Problem

The `rules` repo holds the canonical coding-agent instructions. Several other repos (all under the
same account) adopt those rules. Today, a change to the rules here has to be copied into every other
repo by hand. We want a rule change committed here to propagate to every consuming repo
**automatically, as a pull request** for the repo owner to review and merge.

## Key constraint: not all of `CLAUDE.md` is shareable

`CLAUDE.md` is a placeholder-driven template. Some of it is genuinely **per-repo** (Project overview,
Setup, Commands, Testing, Gotchas, stack-specific style/security notes), and some of it is
**universal** — identical for every repo (Working principles, the working/PR workflow, Conventional
Commits, the generic security principles, the hard "never"s).

A naive "copy the whole file" sync would clobber each repo's filled-in placeholders. So the core of
this design is **separating the two concerns** rather than choosing a smarter copy mechanism.

## Design

### 1. Split universal rules into `shared-rules.md`

Factor the universal, stack-agnostic rules out of `CLAUDE.md` into a new top-level file,
`shared-rules.md`. This file contains **no placeholders** — it is byte-identical in every repo.

Each repo's `CLAUDE.md` keeps only project-specific content and imports the shared file with Claude
Code's import syntax:

```markdown
## Shared rules
@shared-rules.md
```

(The repo already relies on `@`-imports — `@wiki/index.md`, `@CONTRIBUTING.md` — so the mechanism is
proven.) Rules in `shared-rules.md` reference the **Gate** command, which each repo defines in its own
`CLAUDE.md` Commands table — the shared file states the policy, the local file supplies the specifics.

**What moves to `shared-rules.md`** (identical everywhere):
Working principles · Workflow · the "explain *why*, not *what*" code-style rule · Commit messages
(Conventional Commits) · the generic Security & safety principles · Never.

**What stays in `CLAUDE.md`** (per-repo):
Project overview · Setup · Commands (incl. **Gate**) · stack-specific Code style · Testing ·
Branch/PR conventions · domain-specific Security constraints · Gotchas · Further context.

### 2. Deterministic file-sync, push model

Because `shared-rules.md` has no placeholders, the sync needs no intelligence — a plain file copy is
correct and reviewable. A workflow in **this** repo, on push to `main`, copies `shared-rules.md` into
each target repo and opens a PR there, using
[`step-security/repo-file-sync-action`](https://github.com/step-security/repo-file-sync-action) (a
supply-chain-hardened drop-in for the popular `BetaHuhn/repo-file-sync-action`).

- `.github/sync.yml` — lists target repos and the file(s) to sync.
- `.github/workflows/sync-rules.yml` — runs the action on push to `main` (and `workflow_dispatch`).

### 3. One-time setup

1. **Create a fine-grained Personal Access Token** scoped to the target repos with
   `Contents: read/write` + `Pull requests: read/write`.
2. Add it as the `RULES_SYNC_TOKEN` secret in this repo
   (`Settings → Secrets and variables → Actions`).
3. Fill in the real repo names in `.github/sync.yml`.
4. **Pin the action to a full commit SHA** in `sync-rules.yml` before enabling (replace `@v2`).
5. In each consuming repo, add `@shared-rules.md` to its `CLAUDE.md` (the sync delivers the file).

### 4. Onboarding a new repo later

Add one line to `.github/sync.yml`, grant the PAT access to that repo, and add the
`@shared-rules.md` import to its `CLAUDE.md`. That is the whole onboarding.

## Decisions and rejected alternatives

- **Split the file vs. keep one monolithic `CLAUDE.md`.** Splitting is what makes the sync
  deterministic — once the shared content has no placeholders, no merging judgment is needed.
- **Deterministic file-sync vs. agentic apply (run Claude Code in each repo).** The agentic approach
  was only needed to merge a change into a mixed file without clobbering placeholders. Splitting
  removes that need, so we avoid the agent's nondeterminism, API cost, and per-repo API keys. The
  agentic path stays available as a fallback if a future change ever needs real merging judgment.
- **Push model vs. pull model (a workflow in every target repo).** Pull is more decentralized and
  least-privilege, which matters when targets are owned by other people/teams. Here every repo is
  under one account, so pull's main advantage is moot, while push keeps the sync logic in **one**
  place (no N copies to maintain) and propagates on commit without extra `repository_dispatch`
  plumbing. Revisit if the rules are ever adopted by repos outside this account.

## Out of scope

- `AGENTS.md` consumers that don't expand `@`-imports (e.g. some non-Claude tools) won't see the
  shared file inlined. This is a pre-existing property of the repo's `@`-import convention, not
  introduced here; revisit if a non-Claude consumer needs it.
