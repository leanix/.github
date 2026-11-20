---
type: Convention
title: Renovate Automerge Presets
description: How to use automerge.json5, infrastructure-automerge.json5, branch-merge.json, and semantic-automerge.json5 —
  prerequisites, schedules, and trade-offs.
status: stable
generated:
  by: claude-code
  at: '2026-10-28T10:36:50.451818Z'
updated:
  by: claude-code
  at: '2026-10-28T10:36:50.451818Z'
verified: []
tags:
- renovate
- automerge
- preset
- dependencies
- github-actions
- terraform
sources:
- resource: /sources/local-readme-md-94486f8406.md
  id: local-readme-md-94486f8406
  title: README.md
---
# Renovate Automerge Presets

Four presets cover automated dependency merging. All operate on `Europe/Berlin` timezone.

## automerge.json5 — Standard PR Automerge

**Extends:** nothing (base preset)

**What it does:**
- Merges `minor`, `patch`, `digest`, and `lockFileMaintenance` updates automatically.
- Blocks `major` updates from automerge.
- PR creation window: Mon–Fri 07:00–15:59.
- Uses GitHub's platform automerge (`platformAutomerge: true`) — Renovate loses control of `automergeSchedule` once the PR is created; GitHub merges when checks pass.
- `rebaseWhen: "conflicted"` for merge queue compatibility.
- `prCreation: "not-pending"` + `prNotPendingHours: 0` — waits for required checks before creating the PR.

**Prerequisites:**
- "Allow auto-merge" enabled in repo settings.
- GitHub App `renovate-approve` installed.
- Required CI workflows triggered on push to renovate's branches.
- *(If merge queue enabled)* CI workflow trigger on `merge_group:` or push to temporary merge queue branches.

**Usage:**
```json
{ "extends": ["local>leanix/.github//renovate-presets/automerge.json5"] }
```

---

## infrastructure-automerge.json5 — Terraform-Safe Automerge

**Extends:** `automerge.json5`

**Difference:** Disables automerge for managers `terraform`, `terraform-version`, `terragrunt`, `terragrunt-version`. All other update types (GitHub Actions, Docker, npm, etc.) continue to automerge normally.

**When to use:** Infrastructure repositories where Terraform provider/module/CLI version bumps need manual review due to infrastructure impact.

**Prerequisites:** Same as `automerge.json5`.

**Usage:**
```json
{ "extends": ["local>leanix/.github//renovate-presets/infrastructure-automerge.json5"] }
```

---

## branch-merge.json — Branch Automerge (No PR)

**Extends:** nothing

**What it does:**
- Merges `minor`, `patch`, `digest` updates directly to the branch — no PR created.
- Merge window: Mon–Fri 09:00–13:00.

> **Warning:** Does **not** work with merge queues. Use `automerge.json5` instead for repos with merge queues enabled.

**Prerequisites:**
- "Allow auto-merge" enabled in repo settings.
- `renovate[bot]` allowed to bypass branch protection RuleSets on the default branch.

**Usage:**
```json
{ "extends": ["local>leanix/.github//renovate-presets/branch-merge.json"] }
```

---

## semantic-automerge.json5 — Automerge with Semantic Commits

**Extends:** `automerge.json5`

**Additional behaviour:**
- GitHub Actions workflow files → `chore` commit type.
- `devDependencies` → `chore` commit type; updates run **nightly** (16:00–07:59) to avoid consuming CI capacity during the day.
- `dependencies` / `peerDependencies` → `fix` commit type.
- Business-hours window shifted to Mon–Fri 08:00–15:59.

**When to use:** Repos that use conventional commits / semantic versioning and want correct commit types on automated PRs.

**Prerequisites:** Same as `automerge.json5`.

**Usage:**
```json
{ "extends": ["local>leanix/.github//renovate-presets/semantic-automerge.json5"] }
```

---

## Comparison

| | automerge | infrastructure-automerge | branch-merge | semantic-automerge |
|---|---|---|---|---|
| Strategy | PR | PR | Branch | PR |
| Merge queue safe | Yes | Yes | **No** | Yes |
| Terraform automerge | Yes | **No** | Yes | Yes |
| Semantic commits | No | No | No | Yes |
| Dev deps nightly | No | No | No | Yes |

## Related Pages

- [[renovate-presets-overview]] — full preset catalogue
- [[renovate-security-preset]] — OSV vulnerability scanning preset
