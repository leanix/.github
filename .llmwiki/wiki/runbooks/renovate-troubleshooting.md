---
type: Runbook
title: Renovate Troubleshooting
description: 'Known issues, debugging steps, and operational runbooks for Renovate: automerge not triggering, transitive dependency
  pinning, and re-enabling Renovate globally after an emergency disable.'
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
- troubleshooting
- automerge
- runbook
- dependencies
sources:
- resource: /sources/confluence-how-to-automate-dependency-updates-with-renovate-76931b3a5d.md
  id: confluence-how-to-automate-dependency-updates-with-renovate-76931b3a5d
  title: How+to+Automate+Dependency+Updates+with+Renovate
---
# Renovate Troubleshooting

Source: [Confluence — How to Automate Dependency Updates with Renovate](https://leanix.atlassian.net/wiki/spaces/EN/pages/10026713103)

## Known Issues

### Renovate Does Not Merge When Using Branch-Merge / Automerge

**Cause:** CI workflows are not triggered on pushes to Renovate's branches.

**Fix:** Ensure at least one CI job (not just a skipped job) runs on Renovate branch pushes. GitHub counts skipped jobs as passing, but Renovate requires at least one **actual passing** check.

---

### Renovate Merges Only One Update Per Run

**Cause:** If branches must be up-to-date with `main`, the first merged update invalidates all others. Renovate rebases them on its next run.

**Fix:** Enable [merge queues](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/configuring-pull-request-merges/managing-a-merge-queue) on the repository.

---

### Automerge Preset + Merge Queue: Renovate Merges PRs Itself Instead of Using Platform Automerge

**Fix:** Disable the merge queue requirement in your RuleSet → save → re-enable. This has resolved the issue in several cases.

---

### Automerge PRs Merge Outside the Scheduled Window (`automerge.json5`)

**Cause:** `platformAutomerge: true` hands control to GitHub. Renovate only enables the automerge flag during the scheduled window — but GitHub merges the PR the moment all checks pass, which can be outside the window.

This is expected behaviour with `platformAutomerge`.

---

### Automerge With CODEOWNER Review Requirement

**Background:** Org-level branch protection already has a bypass for Renovate bot. For repos with a CODEOWNER review RuleSet:

1. The CODEOWNER RuleSet must have a bypass for `renovate[bot]` (not `renovate-approve`). In some repos the bypass type must be set to **"Exempt"**.
2. A second `checks` rule (no bypass) requires the main CI to pass before merging — this is expected and correct.

---

## Debugging Steps

1. Check **repository settings** → confirm "Allow auto-merge" is enabled.
2. Verify CI workflows run on **Renovate's branches** (`renovate/**`).
3. Review **branch protection rules** / RuleSets that might block Renovate.
4. Check **Renovate logs** in PR comments or the dependency dashboard. ([Log in via GitHub](https://developer.mend.io/github/leanix/))
5. **No dependency dashboard?** Enable the "Issues" feature for the repository.
6. **Renovate configured but no scans?** Force a first scan at `https://developer.mend.io/github/leanix/<repo-name>` → Actions → **Run Renovate scan**.

---

## Runbook: Pin a Transitive Dependency (Node.js / `package.json`)

Useful when lockfile maintenance breaks your build because a transitive dep gets an incompatible update.

**Step 1 — Declare via `overrides` in `package.json`:**

```json
"overrides": {
  "swagger-ui": {
    "react": "18.3.1",
    "react-dom": "18.3.1"
  }
}
```

This promotes the dependency to an explicit entry Renovate can track.

**Step 2 — Restrict updates in `renovate.json`:**

```json
"packageRules": [
  {
    "matchPackageNames": ["react", "react-dom"],
    "allowedVersions": "<19.0.0"
  }
]
```

Renovate now tracks and pins the dependency while lockfile maintenance runs normally for everything else.

---

## Runbook: Disable Renovate Globally (Emergency)

Set `enabled: false` in the `leanix/.github` base preset config. This stops Renovate across all repos that extend it.

---

## Runbook: Re-Enable Renovate Globally After a Disable

After setting the base preset back to `enabled: true`, repos may be stuck in a disabled state. A manual scan must be triggered per repo. For multiple repos, do this programmatically:

**Step 1** — In a terminal with a logged-in GitHub CLI:

```bash
gh repo list leanix --no-archive --limit 750 --json name | pbcopy
```

**Step 2** — Log in to [developer.mend.io](https://developer.mend.io/github/leanix/), open browser dev tools, and run:

```javascript
const repos = /* paste JSON here */;

const triggerRenovate = (repo) =>
  fetch(`https://developer.mend.io/api/repos/github/leanix/${repo}/renovate/job/add`, {
    headers: { "Content-Type": "application/json", "x-app-id": "1" },
    credentials: "include",
    method: "POST",
    body: '{ "selectedBranches": [] }',
  });

for (const { name } of repos) {
  await triggerRenovate(name);
}
```

This triggers a Renovate scan for every non-archived repo in the org.

---

## Related Pages

- [[renovate-setup-and-best-practices]] — setup, best practices
- [[renovate-presets-overview]] — preset catalogue
- [[renovate-automerge-presets]] — automerge known issues
