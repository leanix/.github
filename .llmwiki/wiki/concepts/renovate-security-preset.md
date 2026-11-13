---
type: Policy
title: Renovate Security Preset (security.json5)
description: OSV vulnerability scanning, 5-day minimum release age, and automated security fix merging with CVE severity labels.
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
- security
- osv
- vulnerability
- cve
- preset
sources:
- resource: /sources/local-readme-md-94486f8406.md
  id: local-readme-md-94486f8406
  title: README.md
---
# Renovate Security Preset

File: `renovate-presets/security.json5`

This preset is automatically included via `local>leanix/.github` (the root `default.json`). Apply it explicitly only when you want it without the rest of the defaults.

## What It Does

### OSV Vulnerability Scanning
- `osvVulnerabilityAlerts: true` — experimental feature; enables OSV database scanning for all dependencies.
- `dependencyDashboardOSVVulnerabilitySummary: "all"` — displays OSV alerts in the Renovate dependency dashboard.

### Minimum Release Age (Supply Chain Protection)
- Global: `minimumReleaseAge: "5 days"` with `minimumReleaseAgeBehaviour: "timestamp-optional"`.
- npm overridden explicitly to also be 5 days (avoids `security:minimumReleaseAgeNpm` preset conflict).
- **Exceptions** (no minimum age):
  - `@leanix/*` npm packages — internal packages bypass the wait.
  - `leanix/*` GitHub Actions — internal actions bypass the wait.

### Vulnerability Alerts — Auto-Merge Configuration

Security fix PRs (`vulnerabilityAlerts`) get special treatment:

| Setting | Value | Reason |
|---|---|---|
| `automerge` | `true` | Minimize time-to-fix |
| `platformAutomerge` | `true` | Use GitHub automerge |
| `prCreation` | `"not-pending"` | Wait for checks |
| `schedule` | `[]` | Can be created at any time |
| `groupName` | `null` | No grouping — one PR per vulnerability |
| `dependencyDashboardApproval` | `false` | No manual approval needed |
| `minimumReleaseAge` | `5 days` | Still enforced for security fixes |
| `vulnerabilityFixStrategy` | `"lowest"` | Use minimum version that fixes the CVE |

### CVE Severity Labels

Security PRs are labelled and commit-suffixed with the CVE severity:
- Label: `SEVERITY:{{vulnerabilitySeverity}}`
- Commit suffix: `[SECURITY] [SEVERITY: CRITICAL 🔴]` / `[SEVERITY: MEDIUM 🟡]` (emoji for HIGH/CRITICAL = 🔴, MEDIUM/MODERATE = 🟡)

## Usage

```json
{
  "extends": [
    "local>leanix/.github//renovate-presets/security.json5"
  ]
}
```

Note: applying `local>leanix/.github` already includes this preset — do not duplicate it.

## Related Pages

- [[renovate-presets-overview]] — full preset catalogue
- [[renovate-automerge-presets]] — automerge preset details
