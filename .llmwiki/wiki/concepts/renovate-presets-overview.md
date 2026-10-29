---
type: Reference
title: Renovate Presets — Overview
description: Catalogue of all shareable Renovate configuration presets in leanix/.github, with usage, prerequisites, and combination
  patterns.
status: stable
generated:
  by: claude-code
  at: '2026-10-28T10:36:50.451818Z'
updated:
  by: claude-code
  at: '2026-10-28T10:44:15.544307Z'
verified: []
tags:
- renovate
- dependencies
- automerge
- preset
- configuration
sources:
- resource: /sources/local-readme-md-94486f8406.md
  id: local-readme-md-94486f8406
  title: README.md
- resource: /sources/confluence-how-to-automate-dependency-updates-with-renovate-76931b3a5d.md
  id: confluence-how-to-automate-dependency-updates-with-renovate-76931b3a5d
  title: How+to+Automate+Dependency+Updates+with+Renovate
---
# Renovate Presets — Overview

All presets live in `renovate-presets/` inside the `leanix/.github` repository. Add them to `renovate.json` via the `extends` block.

## Available Presets

| Preset | Type | Key Characteristic |
|---|---|---|
| `automerge.json5` | PR automerge | Merges minor/patch/digest during business hours (Mon–Fri 07–15 Berlin). Requires `renovate-approve` GitHub App. |
| `infrastructure-automerge.json5` | PR automerge | Same as `automerge.json5` but blocks Terraform/Terragrunt automerge. |
| `branch-merge.json` | Branch automerge | **Deprecated — use `automerge.json5` instead.** Merges directly to branch (no PR) Mon–Fri 09–13 Berlin. **Incompatible with merge queues.** |
| `semantic-automerge.json5` | PR automerge + semantic commits | Extends `automerge.json5`; adds `chore`/`fix` commit types; dev deps update nightly. |
| `security.json5` | Security scanning | OSV vulnerability alerts, 5-day minimum release age, auto-merges security fixes with CVE severity labels. |

The root `default.json` (at repo root, not this directory) provides org-wide defaults including pinning and the security preset.

## Reference Syntax

```json
{
  "extends": [
    "local>leanix/.github//renovate-presets/<filename>"
  ]
}
```

## Common Combination: Defaults + Automerge

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "local>leanix/.github",
    "local>leanix/.github//renovate-presets/automerge.json5"
  ]
}
```

`local>leanix/.github` pulls in `default.json` (best practices, pin all deps, 5-day release age, security). Adding an automerge preset on top enables automatic merging of safe updates.

## Choosing a Preset

- **Standard app repo** → `automerge.json5`
- **Infrastructure / Terraform repo** → `infrastructure-automerge.json5`
- **No merge queue, want branch strategy** → ~~`branch-merge.json`~~ **Deprecated** — prefer `automerge.json5`
- **Monorepo with conventional commits** → `semantic-automerge.json5`
- **Need OSV scanning only** → `security.json5` (also included by default via `default.json`)

## Creating a New Preset

1. Create `.json` or `.json5` in `renovate-presets/`
2. Document it in `renovate-presets/README.md`
3. Reference as `local>leanix/.github//renovate-presets/<filename>`

## Related Pages

- [[renovate-setup-and-best-practices]] — org-wide onboarding and best practices
- [[renovate-automerge-presets]] — detailed automerge preset guide
- [[renovate-security-preset]] — security/OSV preset details
- [[renovate-troubleshooting]] — known issues and runbooks
