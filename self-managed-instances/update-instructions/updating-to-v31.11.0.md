# sfops v31.11.0 Migration Guide

## Overview

v31.11.0 introduces a directory restructuring for project workflows. Workflows are now organized into versioned directories (`v1/` and `v2/`) to provide clearer separation and easier maintenance.

## Directory Structure Change

Project workflows have moved from:
```
project-workflows/
├── sfops-close-issue.yml
├── sfops-open-pr.yml
└── ...
```

To:
```
project-workflows/
├── v1/
│   ├── sfops-close-issue.yml
│   ├── sfops-open-pr.yml
│   └── ...
└── v2/
    └── ...
```

---

## Updating v1 Project Workflows

### 1. Pull Latest sfops Release from Upstream

```bash
# Fetch and create PR for v31.11.0
git fetch upstream --tags
git checkout -b sfops-v31.11.0-update
git merge v31.11.0
git push origin sfops-v31.11.0-update

# Create PR via GitHub UI and merge it to main
```

### 2. Copy Workflows from v1 Directory

Copy workflows from `project-workflows/v1/` to your project's `.github/workflows/` directory:

```bash
cp project-workflows/v1/*.yml .github/workflows/
```

### 3. Changes in v1 Workflows

#### 3.1 Close Issue Workflow - New Optional Secrets

The close issue workflow now calls `exec-issue-closer.yml` (previously `close-pending-action-on-issues.yml`) and supports additional environment-specific auth URL secrets for environment cleanup on issue close.

**New optional secrets** (add only for environments you use):

| Secret Name | Description |
|-------------|-------------|
| `STAGING_SFDX_AUTH_URL` | Auth URL for Staging environment |
| `PREPROD_SFDX_AUTH_URL` | Auth URL for Pre-Production environment |
| `UAT_SFDX_AUTH_URL` | Auth URL for UAT environment |
| `QA_SFDX_AUTH_URL` | Auth URL for QA environment |
| `IQA_SFDX_AUTH_URL` | Auth URL for IQA environment |
| `SIT_SFDX_AUTH_URL` | Auth URL for SIT environment |

{% hint style="info" %}
These secrets are optional. Only add them for environments you actually use. The workflow will function without them.
{% endhint %}

#### 3.2 AI Provider Support - Open PR Workflow

The v1 open-pr workflow now supports GitHub Copilot for AI-powered analysis. You can optionally add the `COPILOT_TOKEN` secret if you want to use this feature.

Both `ANTHROPIC_API_KEY` and `COPILOT_TOKEN` are supported by the underlying reusable workflow - add whichever provider(s) you want to use.

#### 3.3 Removed sfp-server References

The following inputs and secrets have been removed from v1 workflows (sfp-server is exclusive to v2):

| Workflow | Removed Inputs | Removed Secrets |
|----------|----------------|-----------------|
| `sfops-cron-every-30-mins.yml` | `sfp-server-url` | `SFP_SERVER_TOKEN` |
| `sfops-open-pr.yml` | `sfp-server-url` | `SFP_SERVER_TOKEN` |
| `sfops-push-to-branch.yml` | `sfp-server-url` | `SFP_SERVER_TOKEN` |

If your existing workflows have these references, they will be removed when you copy the new v1 workflows.

#### 3.4 Release Workflows - Added NPM_TOKEN

The following workflows now include `NPM_TOKEN` secret:

- `sfops-open-release-issue.yml`
- `sfops-open-release-release-any-envs.yml`

This secret should already exist in your repository if you're using package publishing.

#### 3.5 File Rename

`sfops-cron-ci-sandbox-create-v2.yml` has been renamed to `sfops-cron-ci-sandbox-create.yml`

---

## Updating v2 Project Workflows

### 1. Pull Latest sfops Release

Follow the same git pull steps as v1 above.

### 2. Copy Workflows from v2 Directory

```bash
cp project-workflows/v2/*.yml .github/workflows/
```

### 3. Changes in v2 Workflows

#### 3.1 AI Provider Support

The v2 open-pr workflow supports both AI providers. Add the secrets for the provider(s) you want to use:

- `ANTHROPIC_API_KEY` - For Anthropic Claude analysis
- `COPILOT_TOKEN` - For GitHub Copilot analysis

Both are optional - add whichever you prefer to use.

---

## Validation Checklist

### For v1 Users

- [ ] Pulled sfops v31.11.0 from upstream
- [ ] Copied workflows from `project-workflows/v1/` to `.github/workflows/`
- [ ] (Optional) Added environment-specific auth URL secrets for close-issue workflow
- [ ] (Optional) Added `COPILOT_TOKEN` if using GitHub Copilot for AI analysis
- [ ] Verified `NPM_TOKEN` secret exists for release workflows
- [ ] Tested PR validation workflow
- [ ] Tested push-to-branch workflow

### For v2 Users

- [ ] Pulled sfops v31.11.0 from upstream
- [ ] Copied workflows from `project-workflows/v2/` to `.github/workflows/`
- [ ] (Optional) Added AI provider secrets (`ANTHROPIC_API_KEY` and/or `COPILOT_TOKEN`)
- [ ] Tested PR validation workflow
- [ ] Tested push-to-branch workflow

---

## Rollback Plan

If you encounter issues, you can pin workflows to the previous version:

```yaml
uses: your-org/sfops-gh-actions/.github/workflows/pr-validate.yml@v31.10.0
```

Or revert to the previous project workflow files from your git history.
