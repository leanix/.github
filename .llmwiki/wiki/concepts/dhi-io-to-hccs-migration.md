---
type: Process
title: Migrating from dhi.io to HCCS
description: Step-by-step guide for replacing dhi.io base images with HCCS-hosted Docker Hardened Images, including why automation
  is not possible and how Renovate takes over after migration.
status: stable
generated:
  by: claude-code
  at: '2026-10-28T10:41:29.870339Z'
updated:
  by: claude-code
  at: '2026-10-28T10:41:29.870339Z'
verified: []
tags:
- docker
- hccs
- migration
- dhi
- base-image
- renovate
sources:
- resource: /sources/confluence-how-to-work-with-docker-hardened-images-98eaa47eee.md
  id: confluence-how-to-work-with-docker-hardened-images-98eaa47eee
  title: How+to+Work+with+Docker+Hardened+Images
---
# Migrating from dhi.io to HCCS

LeanIX uses HCCS (Hyperspace Central Container Service) as the official DHI mirror. Direct use of `dhi.io` images is not supported.

## Why Migrate?

- `dhi.io` introduces a 3rd-party dependency that is not officially supported.
- Renovate is **not** integrated with the `dhi.io` registry — base image updates would require manual tracking.
- HCCS images are managed by Renovate automatically after migration.

## Can Migration Be Automated?

**No.** The tag structure differs between `dhi.io` and HCCS (HCCS has no generic major-version-only tags). Renovate cannot bridge the two registries to perform the migration automatically.

## How to Migrate

1. Open the [HCCS Image Inventory](https://pages.github.tools.sap/I529077/hardened-image-reporting/) and find the equivalent image with a compatible available tag.
2. Update the `FROM` statement in your `Dockerfile`:

   ```dockerfile
   # Before (dhi.io)
   FROM dhi.io/dhi-node:20-debian

   # After (HCCS)
   FROM <SAP_ARTIFACTORY_URL>/dhi-node:20.x.y-debian-<arch>
   ```

3. Verify your CI/CD pipeline has the HCCS login step in place — see [[docker-hardened-images-hccs]].
4. After merging, Renovate will automatically manage future base image updates.

## Notes

- Images in HCCS are suffixed by architecture. Choose `amd64` unless your workload requires otherwise.
- If the equivalent image isn't yet in HCCS, request it before migrating — see the "Requesting a Missing Image" section in [[docker-hardened-images-hccs]].

## Related Pages

- [[docker-hardened-images-hccs]] — HCCS overview, CI/CD setup, Renovate integration
