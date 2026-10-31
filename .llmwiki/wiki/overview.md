---
type: Module
title: Project Overview
description: Overview of the project represented by this wiki.
status: stable
updated:
  by: llm-wiki-maintainer/gpt-5-6-luna
  at: '2026-10-28T12:54:40.345016Z'
verified: []
tags:
- overview
sources:
- resource: /sources/confluence-how-to-automate-dependency-updates-with-renovate-76931b3a5d.md
  id: confluence-how-to-automate-dependency-updates-with-renovate-76931b3a5d
  title: How+to+Automate+Dependency+Updates+with+Renovate
---
# Project Overview

This page is the entry point for the project wiki.

## Dependency Updates

Repositories using the LeanIX Renovate setup generally extend the organization-wide preset with `local>leanix/.github`. The preset provides recommended Renovate settings, dependency pinning (excluding peer dependencies), a five-day minimum release age, OSV-based vulnerability scanning, and LeanIX-specific package rules. Repository-specific presets and rules may be added when needed.

## Dependency Update Practices

Review the Renovate dependency dashboard weekly, address security vulnerabilities promptly, run the test suite on Renovate pull requests, review major updates carefully, and group related updates only when doing so does not make debugging or rollback harder. Low-risk minor, patch, and digest updates may use the optional automerge preset when CI, repository settings, branch protection, and merge-queue requirements are satisfied.

## Operational Notes

Renovate branches must trigger at least one actual passing CI status check for automated merging. If scanning does not start, enable repository issues and trigger an initial Renovate scan through the Mend developer interface. For temporary organization-wide suspension, the base preset can disable Renovate; repositories may require a manual scan after it is re-enabled.

