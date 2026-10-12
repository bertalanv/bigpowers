# Fork Operations — bertalanv/bigpowers

This file is the **only tracked file** in `personal/` (see the `.gitignore` exception).
It documents how this fork is operated — for humans and for cold-start agents on any
machine, including the 10-VM swarm. Everything else under `personal/` is local-only.

## Why this fork exists

Upstream bigpowers is the base methodology. This fork is the **single runtime** for a
personal 10-VM agentic swarm: every machine installs the pi package from `origin/main`,
so this fork is the fleet's source of truth. It is deliberately kept as close to
upstream as possible — personal changes are few, deliberate, and tracked in
`personal/VERSION.md`.

## Topology

```
upstream → danielvm-git/bigpowers   (base — upstream publishes here)
origin   → bertalanv/bigpowers      (this fork — the fleet tracks its main)
```

- One local clone, two named remotes.
- Local `main` = upstream + personal commits, linear history.
- The pi package `git:github.com/bertalanv/bigpowers` resolves `main` at
  install/update time — repo identity is URL-based, so moving the local clone
  changes nothing (done 2026-10-12: `~/Documents/notebooks/bigpowers` → `~/dev/bigpowers`,
  to leave the iCloud-sync zone).

## Fleet install & update (per machine)

```bash
# once per machine (requires pi + Node.js)
pi install git:github.com/bertalanv/bigpowers

# whenever main moves
pi update git:github.com/bertalanv/bigpowers   # or: pi use update --extensions
pi list    # verify: shows git:github.com/bertalanv/bigpowers
```

Don't pin a tag or commit — the install tracks `main`, so updates follow pushes.

## Syncing upstream (manual, review built in)

```bash
git fetch upstream
git log --oneline main..upstream/main   # 1. what's new — read it
git diff --stat main...upstream/main    # 2. scope — where it touches
git tag pre-sync main                   # 3. rollback anchor
git rebase upstream/main                # 4. conflicts = your accept/reject points
bash scripts/validate-doctrine.sh       # 5. trust the merge only after gates
git push --force-with-lease origin main # 6. publish to fork + fleet
```

- **Reject an upstream change** to a file you never touched: `git checkout pre-sync -- <file>`,
  commit the override, push. Your override becomes a permanent part of the fork's diff —
  expected, and recorded in `VERSION.md`.
- **Bad sync:** `git reset --hard pre-sync && git push --force-with-lease origin main`.
- `personal/sync-upstream.sh` will automate this with a review stop (not yet written).

## What this fork changes vs upstream

The per-change ledger lives in **`personal/VERSION.md`** (local-only, per machine):
what each change is, the landing commit, and whether it has an open/merged upstream PR.

Verify state anytime:

```bash
git log --oneline upstream/main..main            # what the fork carries
gh pr list --repo danielvm-git/bigpowers         # upstream PR status
```

## Ground rules

- Keep the diff vs upstream small and deliberate; record every change in `VERSION.md`.
- Upstream-worthy fixes go as PRs to `danielvm-git/bigpowers` — branch off
  `upstream/main` (never off your `main`), push the branch to `origin`,
  `gh pr create --repo danielvm-git/bigpowers --head bertalanv:<branch> --base main`.
  They land on the fork via the next rebase, not a merge.
- Fork-only changes (this README, `VERSION.md`, personal tooling) live under `personal/`.
- No direct work on `main`; run `bash scripts/run-verification-gates.sh` before pushing.
