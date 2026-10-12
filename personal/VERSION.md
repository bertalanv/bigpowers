# Bigpowers Fork — Change Tracker

Personal tracking of every change made in this fork (`bertalanv/bigpowers`).
Not part of upstream bigpowers; not injected into any LLM context.

**Topology**
- `origin` = `github.com/bertalanv/bigpowers` — this fork; the fleet's pi package tracks its `main`
- `upstream` = `github.com/danielvm-git/bigpowers` — the base we stay close to

**How to update this file:** when a change lands, add a row + a detail block.
Verify state with:

```bash
git log --oneline upstream/main..main          # what you carry vs upstream
gh pr list --repo danielvm-git/bigpowers       # upstream PR status
```

## Status at a glance

| # | Change | Fork main | Upstream PR | Notes |
|---|--------|-----------|-------------|-------|
| 1 | Remove pre-lifecycle root working notes | ✅ merged `ef691a40` | ❌ none (fork-only) | permanent 3-file diff |
| 2 | Stop tracking generated allure output | ✅ merged `c0294168` | ✅ #144 open | + `.gitignore` entry |
| 3 | Sync package-lock version → 2.88.9 | ✅ merged `443eaa50` | ✅ #145 open | 2-line lockfile fix |
| 4 | Remove story-tag headers from skills | ✅ merged `e3b91b68` | 📝 drafted, not yet opened | 97 lines / 40 files |
| 5 | This tracker (`personal/VERSION.md`) | (this commit) | ❌ none (fork-only) | personal bookkeeping |

## Details

### 1. Remove pre-lifecycle root working notes — `ef691a40`
- **What:** deleted `ANALISE_CONSOLIDACAO.md`, `GSD_WORKFLOW_ANALYSIS_VS_BIGPOWERS.md`, `VALIDACAO_CONTRA_GSD.md` (1,528 lines) from repo root.
- **Why:** pre-lifecycle analysis leftovers; out of place in the published repo.
- **Fork:** merged direct to `main` (before the git guard activated).
- **Upstream:** PR #143 was opened then closed at owner request — fork-only change.
- **Diff impact:** permanent (3 files, −1,528 lines).

### 2. Stop tracking generated allure test output — `c0294168`
- **What:** `git rm` the 3 files under `allure-results/` (`categories.json`, `executor.json`, `junit-results.xml`) + added `allure-results/` to `.gitignore`.
- **Why:** generated test reports were committed and drifting; the directory should be local output only.
- **Fork:** merged to `main`.
- **Upstream:** PR #144 open.

### 3. Sync package-lock version to 2.88.9 — `443eaa50`
- **What:** `package-lock.json` root `version` field `2.88.3` → `2.88.9` (2 lines).
- **Why:** lockfile lagged 6 patch releases behind `package.json` / git tag `v2.88.9` / GitHub Release; stale version invites spurious lockfile churn on `npm install`.
- **Fork:** merged to `main`.
- **Upstream:** PR #145 open.

### 4. Remove story-tag headers from SKILL.md files — `e3b91b68`
- **What:** deleted 97 `# story: eNNsNN` provenance heading lines from 40 `skills/*/SKILL.md` sources; regenerated all 16 adapter dirs + traceability matrix/wikis. Landed via fork PR `bertalanv/bigpowers#1` (squash).
- **Why:** pi (and other agents) inject the full SKILL.md body into the LLM context on every skill invocation. The headings are this repo's internal traceability metadata — to a model working in *your* project they read as false state declarations ("we are on e82").
- **Safety:** tags' only consumer is `trace-stories.sh`; every affected story keeps other evidence (planning docs, implementation files, heuristics). Verified: `trace-stories.sh --strict` green, 0 dark stories, doctrine/specs gates green, compliance 97/98 (one pre-existing unrelated fail).
- **Deliberately kept:** tags in `skills/seed-conventions/REFERENCE.md` and `security-review/fixtures/*` (not injected as skill instructions) → 2 tag lines still ride in the seed-conventions pi adapter.
- **Fork:** merged to `main` (fork PR #1, squash `e3b91b68`).
- **Upstream:** PR body drafted & approved-in-principle; not yet opened (next free number; #146 was taken by an unrelated upstream PR).
- **Diff impact:** the big one — ~380 files, but ~95% is mechanical adapter/matrix regeneration; decision-level delta = 97 lines.

### 5. This tracker — `personal/VERSION.md`
- Fork-only bookkeeping. Lives in `personal/` (the reserved area for fork-only tooling; also planned home for `sync-upstream.sh` / `fleet-update.sh`).
- Not upstreamed, not shipped into LLM context.

## Local-only (never committed, no diff impact)

- ~550 untracked macOS-style duplicate files (`… 2.md`, `… 3.md` across `.pi/`, `.gemini/`, `allure-results/`) — orphan copies, removed via `sync-skills.sh`'s stale-file sweep + manual cleanup. Untracked ⇒ invisible to git ⇒ never a diff.

## Pending / next

- [ ] Open upstream PR for change 4 (story-tags) — body drafted in session notes
- [ ] Rebase `main` onto `upstream/main` when #144/#145 (or the story-tags PR) merge
- [ ] `personal/sync-upstream.sh` — fetch → show new commits → rebase → gates → push
- [ ] `personal/fleet-update.sh` — fan out `pi update --extensions` to the 10 VMs (needs: VM access method + pi installed everywhere)
- [ ] Fleet rollout: one-time `pi install git:github.com/bertalanv/bigpowers` per VM
