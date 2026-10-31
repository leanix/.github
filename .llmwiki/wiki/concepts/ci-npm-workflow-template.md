---
type: Reference
title: CI Workflow Template — npm (test-release-report)
description: Standard GitHub Actions CI workflow template for npm projects released as Docker images. Delegates to the pinned
  leanix/shared-github-workflows reusable workflow.
status: stable
generated:
  by: claude-code
  at: '2026-10-28T10:39:47.791364Z'
updated:
  by: claude-code
  at: '2026-10-28T10:39:47.791364Z'
verified: []
tags:
- ci
- github-actions
- npm
- workflow-template
- reusable-workflow
sources:
- resource: /sources/local-ci-npm-yml-8a8e1b4f8f.md
  id: local-ci-npm-yml-8a8e1b4f8f
  title: ci.npm.yml
---
# CI Workflow Template — npm (test-release-report)

File: `workflow-templates/ci.npm.yml`  
Metadata: `workflow-templates/ci.npm.properties.json`

This is a [GitHub workflow template](https://docs.github.com/en/actions/using-workflows/creating-starter-workflows-for-your-organization) — it appears in the GitHub UI under **Actions → New workflow** for any repo in the `leanix` org that contains a `package.json` or `.nvmrc` file.

## What It Does

Delegates entirely to a pinned reusable workflow:

```
leanix/shared-github-workflows/.github/workflows/test-release-report.npm.reusable.yml
@ e1282e41e08bf67c4e6835c20c07c85e3254f5ae
```

All CI logic (test, release, report) lives in that reusable workflow. This template is a thin caller.

## Triggers

| Event | Conditions |
|---|---|
| `push` | Default branch only (`$default_branch`) |
| `pull_request` | `opened`, `ready_for_review`, `reopened`, `synchronize` |

Draft PRs are skipped via `if: ${{ !github.event.pull_request.draft }}`.

## Permissions

| Permission | Level | Reason |
|---|---|---|
| `contents` | `write` | Release commits/tags |
| `actions` | `write` | Workflow dispatch / cancel |
| `packages` | `read` | Pull from GHCR |

## Inputs Passed to Reusable Workflow

| Input | Value |
|---|---|
| `node-version-file` | `.nvmrc` |

Secrets are forwarded with `secrets: inherit`.

## Environment Variables

`ACTION_URL` is set globally to the current run URL (`https://github.com/${{ github.repository }}/actions/runs/${{ github.run_id }}`), available to all steps in the called workflow.

## Applying to a Repository

1. Go to **Actions → New workflow** in the target repo.
2. Select **npm CI (test-release-report)** (shown for repos with `package.json` or `.nvmrc`).
3. Commit the generated `ci.npm.yml` to `.github/workflows/`.
4. Ensure `.nvmrc` exists at the repo root specifying the Node.js version.

## Updating the Pinned Commit

The reusable workflow is pinned to a specific SHA (`e1282e41e…`). To update:

1. Find the new SHA in `leanix/shared-github-workflows`.
2. Update the `uses:` line in `workflow-templates/ci.npm.yml`.
3. Repos that already adopted the template will need to pull the updated reference manually — the template is copied at adoption time, not linked.

## GitHub UI Metadata (`ci.npm.properties.json`)

```json
{
  "name": "npm CI (test-release-report)",
  "description": "Standard CI workflow for npm projects released as Docker images.",
  "iconName": "check-circle-fill",
  "categories": ["JavaScript", "TypeScript"],
  "filePatterns": ["package.json$", ".nvmrc$"]
}
```

`filePatterns` controls which repos see this template in the GitHub UI suggestion list.
