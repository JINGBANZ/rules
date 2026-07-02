# AGENTS.md

This repo is the source of truth for shared coding-agent rules and the starter files consuming repos
copy. Everything here is markdown plus workflow config — there is no build, test suite, or Gate.

## Layout

| Path | What it is |
| --- | --- |
| `shared-rules.md` | The universal rules — the synced artifact. The reusable sync workflow splices this file into the `shared-rules` marker block of each consuming repo's `AGENTS.md`. |
| `templates/` | Starter files consumers copy verbatim: the `AGENTS.md` seed, the one-line `CLAUDE.md` shim, and the `wiki/` starter. |
| `README.md` | How the sync works, how to adopt the rules in a repo, and authoring guidance. |

The sync machinery itself lives in [`JINGBANZ/workflows`](https://github.com/JINGBANZ/workflows):
the reusable workflow (`.github/workflows/sync-shared-rules.yml`) and the per-repo caller stub
(`templates/sync-shared-rules.yml`). This repo holds only the content being synced.

## Rules for working here

- Everything under `templates/` is a **template shipped to consumers, not this repo's live
  documentation**. The `<angle-bracket>` hints are intentional — never fill them in, and never apply
  the `templates/wiki/` keep-in-sync checklist to this repo (it has no wiki of its own).
- Keep `shared-rules.md` universal: no placeholders, nothing stack- or project-specific.
  Project-specific guidance belongs in the seed sections of `templates/AGENTS.md` instead.
- The marker block in `templates/AGENTS.md` ships **empty** — the first sync run in a consuming repo
  populates it. Never paste the rules into it; that would duplicate `shared-rules.md`.
- The marker format (`<!-- shared-rules:begin -->` / `<!-- shared-rules:end -->`) is coupled to the
  splice step in the reusable workflow in `JINGBANZ/workflows` — change them together or not at all.
- Add a rule only when an agent makes a mistake that rule would have prevented; prune rules that no
  longer earn their place. Every line costs context in every session of every consuming repo.
- Commit messages follow [Conventional Commits](https://www.conventionalcommits.org/). Open a PR for
  every change; never commit to `main`.
