# Renovate Presets

This directory contains shareable Renovate configuration presets that can be used across multiple repositories in your organization. The presets can be added to the `renovate.json` file in the `extends` block.

## Available Presets

### branch-merge.json

**Purpose**: Enables automatic branch merging for low-risk updates during business hours.

**Features**:
- Automatically merges minor, patch, and digest updates
- Operates on Berlin timezone (Europe/Berlin)
- Merge window: Monday-Friday, 9:00-13:00
- Uses branch automerge strategy (merges without creating PRs)

**Prerequisites**

- "Allow auto-merge" enabled in repository settings
- Required CI workflows triggered on push to renovate's branches
- Allow `renovate[bot]` to bypass custom RuleSets protecting the default branch

**Usage**:
```json
{
  "extends": [
    "github>leanix/.github//renovate-presets/branch-merge.json"
  ]
}
```

**When to use**:
- Repositories with comprehensive test coverage
- Projects where minor/patch updates are considered safe
- Teams that want to reduce PR noise for routine updates

### security.json5

> [!NOTE]
> This preset is part of the LeanIX renovate preset

**Purpose**: Integrates OSV (Open Source Vulnerabilities) database to enhance security monitoring.

**Features**:
- Displays OSV vulnerability alerts in the Renovate dependency dashboard
- Enables OSV vulnerability scanning for all dependencies (experimental feature)
- Provides comprehensive vulnerability information from the [OSV database](https://osv.dev/)
- Labels PRs with the CVE severity
- suffixes commits and PR title with the CVE severity

**Usage**:
```json
{
  "extends": [
    "github>leanix/.github//renovate-presets/security.json5"
  ]
}
```

**When to use**:
- All repositories that want enhanced security vulnerability detection
- Projects that need to comply with security standards
- Teams that want proactive security alerts beyond standard vulnerability databases

## Default Preset

The main [default.json](../default.json) preset in the repository root provides organization-wide defaults:

**Features**:
- Best practices configuration from Renovate
- Pin all dependencies except peer dependencies
- 5-day minimum release age for stability (with timestamp-optional fallback)
- Strict internal checks
- Custom package rules:
  - Excludes legacy Kubernetes client versions
  - Restricts Liquibase to versions below 5.0
  - Bypasses minimum release age for internal `@leanix/*` packages

**Usage**:
```json
{
  "extends": [
    "github>leanix/.github"
  ]
}
```

This automatically applies the default configuration and security preset.

## Combining Presets

You can combine multiple presets to build your ideal configuration:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "github>leanix/.github",
    "github>leanix/.github//renovate-presets/branch-merge.json"
  ]
}
```

This example inherits the organization defaults and adds automatic branch merging for minor/patch updates.

## Creating Custom Presets

To add a new preset:

1. Create a new `.json` or `.json5` file in this directory
2. Define your Renovate configuration options
3. Document the preset in this README
4. Reference it using: `github>leanix/.github//renovate-presets/<filename>`

## Resources

- [Renovate Documentation](https://docs.renovatebot.com/)
- [Config Presets](https://docs.renovatebot.com/config-presets/)
- [OSV Database](https://osv.dev/)
- [Shareable Config Presets](https://docs.renovatebot.com/config-presets/#github)
