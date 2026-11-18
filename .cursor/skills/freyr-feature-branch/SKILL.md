---
name: freyr-feature-branch
description: >-
  Create PROD and UAT feature branches across Freyr-TL repositories from
  latest environment base branches, push them, checkout PROD branches for
  local work, and sync the commons submodule in backend-core. Use when the
  user asks to create a new feature branch, set up branches for PROD/UAT, or
  branch off prod/uat bases across freyr-fep-backend-core, freyr-fep-web-app,
  freyr-fep-backend-notifications, or freyr-fep-commons.
---

# Freyr Feature Branch

Create paired PROD and UAT feature branches across selected Freyr-TL repos from freshly fetched and pulled environment base branches, push them to origin, check out PROD branches for immediate work, and sync the commons submodule in backend-core when applicable.

## Repos root

All sibling repos live under:

```
/Users/macbook/Documents/Freyr-TL repos/
```

Read current PROD and UAT base branches and submodule mapping from [repo-config.md](repo-config.md) before every run.

## Hard rules

- Do not run git commands until the user approves the branch plan.
- **Never** run destructive git commands (`reset --hard`, `clean -fdx`, branch deletion, force-push, history rewrite) without explicit approval.
- If the working tree is dirty, **stash first** (`git stash push -m "pre-feature-branch-<name>"`) and note the stash in the report. Do not discard work.
- If a branch already exists locally or on origin, **stop for that repo** and ask whether to skip, use a different name, or delete (only with explicit approval).
- After creating each branch, **push to origin** (`git push -u origin <branch>`). Pushing is required so submodule checkouts can track remote branches.
- Push uses `-u` to set upstream; never force-push unless explicitly approved.
- Request `git_write` and `network` (or `all`) permissions for fetch, pull, branch creation, and push.
- **Never** update git config.
- Submodule sync only touches `services/freyr-fep-commons` in backend-core. Skip notifications commons submodules entirely.

## Workflow

Copy this checklist and track progress:

```
Task Progress:
- [ ] Step 1: Gather inputs (repos + branch name)
- [ ] Step 2: Show plan and get approval
- [ ] Step 3: Create branches (commons first if selected)
- [ ] Step 4: Push all created branches to origin
- [ ] Step 5: Checkout PROD feature branch in each selected repo
- [ ] Step 6: Update commons submodule in backend-core (if applicable)
- [ ] Step 7: Report results
```

### Step 1 — Gather inputs

Ask the user for:

1. **Repositories** (multi-select from):
   - `freyr-fep-backend-core`
   - `freyr-fep-web-app`
   - `freyr-fep-backend-notifications`
   - `freyr-fep-commons`
2. **Feature branch name** (e.g. `feature/my-ticket-123`)

Use `AskQuestion` when available. If the user already provided repos or a branch name in their message, use those and only ask for what is missing.

Read [repo-config.md](repo-config.md) for each repo's PROD and UAT base branches.

### Step 2 — Confirm before executing

Show a summary table for the selected repos only:

| Repo | PROD base → new branch | UAT base → new branch | Post-setup |
|------|------------------------|------------------------|------------|
| freyr-fep-backend-core | `<prod-base>` → `<name>` | `<uat-base>` → `<uat-name>` | checkout PROD branch; sync submodule if commons selected |
| freyr-fep-commons | `<prod-base>` → `<name>` | `<uat-base>` → `<uat-name>` | checkout PROD branch |
| freyr-fep-web-app | `<prod-base>` → `<name>` | `<uat-base>` → `<uat-name>` | checkout PROD branch |
| freyr-fep-backend-notifications | `<prod-base>` → `<name>` | `<uat-base>` → `<uat-name>` | checkout PROD branch (no commons sync) |

Where:

- **PROD branch name** = the name the user provided, as-is.
- **UAT branch name** = `uat-` + user-provided name, unless the name already starts with `uat-` (then use as-is; do not double-prefix).

**Submodule guard:** If `freyr-fep-backend-core` is selected but `freyr-fep-commons` is not, warn:

> Submodule sync requires commons to be branched. Add `freyr-fep-commons` to the selection?

Do not proceed with submodule sync until commons is included or the user explicitly opts out.

Wait for approval before running any git commands.

### Step 3 — Create branches per repo

Process **`freyr-fep-commons` first** when it is in the selection, then the remaining repos. This ensures commons branches exist before parent repos need them.

For each selected repo, run commands in its directory:

```
/Users/macbook/Documents/Freyr-TL repos/<repo-name>/
```

**PROD branch:**

```bash
cd "/Users/macbook/Documents/Freyr-TL repos/<repo>"
git fetch origin
git checkout <prod-base>
git pull origin <prod-base>
git checkout -b <feature-name>
```

**UAT branch:**

```bash
git checkout <uat-base>
git pull origin <uat-base>
git checkout -b <uat-branch-name>
```

Process repos sequentially so failures are easy to diagnose. If one repo fails, report it and continue with the remaining repos unless the user says to stop.

### Step 4 — Push all created branches

After creating each branch (PROD and UAT), push immediately:

```bash
git push -u origin <branch-name>
```

If push fails (branch exists on remote, auth error, etc.), stop for that repo and report — do not continue submodule sync on stale state.

### Step 5 — Checkout PROD for working state

After all pushes complete, return every **selected** repo to its **PROD feature branch** (not UAT, not the environment base):

```bash
cd "/Users/macbook/Documents/Freyr-TL repos/<repo>"
git checkout <feature-name>
git pull origin <feature-name>
```

This is the branch you work on locally. UAT branches remain on origin until you switch to them later.

### Step 6 — Commons submodule sync (backend-core only)

**When:** `freyr-fep-backend-core` **and** `freyr-fep-commons` are both in the selected repos.

**When not:** skip (e.g. web-app only, or backend-core without commons and user opted out).

Run from backend-core root (already on PROD feature branch from Step 5):

```bash
cd "/Users/macbook/Documents/Freyr-TL repos/freyr-fep-backend-core/services/freyr-fep-commons"
git fetch origin
git checkout <feature-name>
git pull origin <feature-name>
cd "/Users/macbook/Documents/Freyr-TL repos/freyr-fep-backend-core"
git add services/freyr-fep-commons
```

**Do not commit** the submodule bump automatically — stage only so you can include it in your first feature commit.

If submodule checkout fails (branch not on remote), re-check that commons was pushed in Step 4 before retrying.

### Step 7 — Report

Return a concise per-repo summary:

- Branches created and pushed (PROD + UAT per repo)
- PROD and UAT base branch commit SHAs after pull
- **Current branch** in each selected repo (should be PROD feature branch)
- Submodule path + commit SHA if backend-core submodule was updated
- Whether `services/freyr-fep-commons` gitlink is staged in backend-core
- Any stashes created
- Any failures or skipped repos

## Branch naming

| User input | PROD branch | UAT branch |
|---|---|---|
| `feature/loan-fix` | `feature/loan-fix` | `uat-feature/loan-fix` |
| `uat-loan-fix` | `uat-loan-fix` | `uat-loan-fix` (no double prefix) |
| `my-feature` | `my-feature` | `uat-my-feature` |

## Examples

**Example 1 — user specifies repos and name:**

> Create feature branch `feature/loan-tracker-fix` for backend-core and commons

Agent reads repo-config, shows plan:

| Repo | PROD base → new branch | UAT base → new branch | Post-setup |
|------|------------------------|------------------------|------------|
| freyr-fep-backend-core | `prod-revert-16-feb` → `feature/loan-tracker-fix` | `uat-jan16` → `uat-feature/loan-tracker-fix` | checkout PROD; sync submodule |
| freyr-fep-commons | `prod-copy-24-09-25-9-30-pm` → `feature/loan-tracker-fix` | `uat-jan16` → `uat-feature/loan-tracker-fix` | checkout PROD |

After approval:

1. Branch + push commons (`feature/loan-tracker-fix`, `uat-feature/loan-tracker-fix`)
2. Branch + push backend-core (same names)
3. Checkout `feature/loan-tracker-fix` in both repos
4. In `services/freyr-fep-commons`: checkout `feature/loan-tracker-fix`, `git add` from parent

**Example 2 — user asks without details:**

> I need a new feature branch for PROD and UAT

Agent asks which repos and the branch name, then follows the workflow.

**Example 3 — dirty working tree:**

Before switching branches, agent runs `git status`. If dirty, stashes with message `pre-feature-branch-<name>`, notes the stash in the report, then proceeds.

## Updating base branches

When PROD or UAT environment branches change, edit [repo-config.md](repo-config.md) only — do not change the workflow in this file unless the process itself changes.
