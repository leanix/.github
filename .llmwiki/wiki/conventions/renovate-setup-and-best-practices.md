---
type: Convention
title: Renovate Setup & Best Practices
description: 'Org-wide Renovate onboarding: minimal renovate.json, what the LeanIX base preset includes, preset selection
  guide, best practices, and security guidelines.'
status: stable
generated:
  by: claude-code
  at: '2026-10-28T10:44:15.544307Z'
updated:
  by: claude-code
  at: '2026-10-28T10:44:15.544307Z'
verified: []
tags:
- renovate
- dependencies
- onboarding
- best-practices
- automerge
sources:
- resource: /sources/confluence-how-to-automate-dependency-updates-with-renovate-76931b3a5d.md
  id: confluence-how-to-automate-dependency-updates-with-renovate-76931b3a5d
  title: How+to+Automate+Dependency+Updates+with+Renovate
---
# Renovate Setup & Best Practices

Renovate is the LeanIX standard for automated dependency updates. It runs org-wide; most repos need only a minimal config file.

## Minimal Setup (Required for All Repos)

Every repository must have a `renovate.json` at the repo root:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["local>leanix/.github"]
}
```

New repositories already have this in place. The `local>leanix/.github` preset includes:

- **Best practices** — Renovate's recommended settings
- **Dependency pinning** — all deps pinned except peer dependencies
- **Supply chain protection** — 5-day minimum release age
- **Security scanning** — OSV vulnerability database integration
- **LeanIX-specific rules** — custom handling for internal `@leanix/*` packages

## Extending the Config

Add presets or custom rules on top of the base:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "local>leanix/.github",
    "local>leanix/.github//renovate-presets/automerge.json5"
  ],
  "reviewers": ["@your-team"],
  "schedule": ["before 9am on monday"]
}
```

See [[renovate-presets-overview]] for the full preset catalogue.

## Available Presets (Summary)

| Preset | Status | Purpose |
|---|---|---|
| `local>leanix/.github` | **Required** | Org defaults, security, pinning |
| `automerge.json5` | Optional | PR automerge — merge queue compatible |
| `infrastructure-automerge.json5` | Optional | Automerge excluding Terraform |
| `semantic-automerge.json5` | Optional | Automerge + conventional commits |
| `branch-merge.json` | **Deprecated** — use `automerge.json5` | Branch automerge — not merge queue safe |
| `security.json5` | Included in default | OSV scanning (explicit use rarely needed) |

## Best Practices

### Repository Maintenance
- Review the dependency dashboard weekly.
- Address security vulnerabilities promptly.
- Keep `renovate.json` as simple as possible.

### PR Management
- Run your test suite on all Renovate PRs.
- **Enable automerge** for low-risk updates — this significantly reduces PR review noise. See [[renovate-automerge-presets]].
- Group related updates when it makes sense — but **do not group to reduce PR load**: it makes debugging and rollbacks much harder.
- You can scope Renovate to a subset of dependencies (e.g. GitHub Actions only, no npm) if test coverage is incomplete.

### Security
- Never override security-related config from the org preset.
- Update dependencies with known vulnerabilities promptly.
- Review major version updates carefully — they are never automerged.
- Use the dependency dashboard to monitor security status.

## Notes on `renovate-approve`

The `renovate-approve` GitHub App is installed on all repos. It auto-approves PRs on creation when the dependency is marked for automerge. PRs opened **before** the automerge setup was adopted must be approved manually.

PRs that are **never** automerged:
- Pinning dependencies (can cause issues in Terraform Cloud scenarios)
- Major version updates

## Getting Help

- **Internal questions:** Slack `#team-butterfly`
- **Config validation:** [Renovate config validator](https://docs.renovatebot.com/config-validation/)
- **Governance dashboards:** [developer.mend.io](https://developer.mend.io/github/leanix/)

## Related Pages

- [[renovate-presets-overview]] — full preset catalogue
- [[renovate-automerge-presets]] — detailed automerge guide
- [[renovate-security-preset]] — OSV scanning details
- [[renovate-troubleshooting]] — known issues and runbooks
