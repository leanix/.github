---
type: Reference
title: Docker Hardened Images & HCCS
description: 'LeanIX/SAP''s HCCS mirror of Docker Hardened Images: CI/CD login setup, Renovate integration, image tagging,
  and how to request missing images.'
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
- security
- hccs
- base-image
- github-actions
- supply-chain
sources:
- resource: /sources/confluence-how-to-work-with-docker-hardened-images-98eaa47eee.md
  id: confluence-how-to-work-with-docker-hardened-images-98eaa47eee
  title: How+to+Work+with+Docker+Hardened+Images
---
# Docker Hardened Images & HCCS

**Docker Hardened Images (DHI)** are curated base images with a reduced attack surface: regularly scanned, patched, signed with verified provenance, and compliant with CIS/DISA STIGs.

## LeanIX/SAP Mirror: HCCS

SAP hosts **Hyperspace Central Container Service (HCCS)** — a private DHI mirror. Use HCCS instead of `dhi.io` directly:

- Images are prefixed with `dhi-`
- Registry URL: available via org variable `${{ vars.SAP_ARTIFACTORY_URL }}`
- [HCCS Image Inventory](https://pages.github.tools.sap/I529077/hardened-image-reporting/)

> **Tag scheme differs from dhi.io** — no generic major-version-only tags. Always check the Image Inventory for available tags.

## DHI vs. Open-Source Images

| | Docker Hardened Images | Open-Source Images |
|---|---|---|
| Security scanning | Regular scanning & patching | May contain outdated vulnerabilities |
| Attack surface | Reduced (minimal base OS) | Larger |
| Compliance | CIS, DISA STIGs | May not meet requirements |
| Supply chain | Signed & verified provenance | No guarantee |
| Renovate support | ✅ via HCCS | ❌ dhi.io not integrated |

## Local Dev Setup

Follow the [official HCCS setup guide](https://leanix.atlassian.net/wiki/spaces/EN/pages/10610999602). Use your SAP account — no technical user needed.

## CI/CD Integration (GitHub Actions)

### Centrally Managed Reusable Pipelines

HCCS support added in commit `d1bd52ce759725b30d23887c4994fa38ae0fd2a7` of the shared pipelines. No manual steps needed if your repo uses those.

### Pipelines Using `leanix/release-docker-image-action`

Update the action to version `de59ec48f3b1804a2b2b51f404026e3d7546c722` or newer. Credentials:

- Username: `${{ vars.SAP_ARTIFACTORY_USER }}` (org variable)
- Token: obtain via `leanix/secrets-action`, reference as `${{ env.SAP_ARTIFACTORY_TOKEN }}`

### Other Pipelines — Login Step

Add a Docker login step before any `docker pull`/`docker build`:

```yaml
- name: Artifactory login (Docker)
  uses: docker/login-action@v4
  with:
    registry: ${{ vars.SAP_ARTIFACTORY_URL }}
    username: ${{ vars.SAP_ARTIFACTORY_USER }}
    password: ${{ env.SAP_ARTIFACTORY_TOKEN }}
```

Credentials:
- `${{ vars.SAP_ARTIFACTORY_URL }}` — HCCS registry URL (org variable)
- `${{ vars.SAP_ARTIFACTORY_USER }}` — login username (org variable)
- `${{ env.SAP_ARTIFACTORY_TOKEN }}` — via `leanix/secrets-action`

## Renovate Integration

The centrally managed Renovate configuration supports HCCS-hosted images. Base image updates are tracked automatically — watch for Renovate PRs updating the `FROM` line in your Dockerfile.

## Requesting a Missing Image

If the image you need isn't in the HCCS inventory:

1. Find it in the [DHI Catalog](https://dhi.io).
2. Submit a request to the HCCS team with: image name, tag/version, use case.
3. The Hyperspace team adds it and notifies you when available.

## Resources

- [Confluence source page](https://leanix.atlassian.net/wiki/spaces/EN/pages/10610999602)
- [HCCS Image Inventory / Adoption Progress](https://pages.github.tools.sap/I529077/hardened-image-reporting/)
- [Docker Hardened Images Documentation](https://docs.docker.com/hardened-images/)
- HCCS Community mailing list: DL HCCS Community

## Related Pages

- [[dhi-io-to-hccs-migration]] — migrate existing Dockerfiles from dhi.io to HCCS
