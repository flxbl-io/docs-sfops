# sfops v31.11.0 Migration Guide

## Prerequisites

This release requires **sfp-pro 50.1.0** or later:

- Update to sfp-pro release: [release-20251218](https://source.flxbl.io/flxbl/sfp-pro/releases/tag/release-20251218)
- Rebuild your sfops Docker images after updating sfp-pro

---

## Updating v1 Project Workflows

### Step 1: Create a Backup Tag

Before making any changes, create a tag so you can easily rollback if needed:

```bash
git tag pre-v31.11.0-backup
git push origin pre-v31.11.0-backup
```

### Step 2: Pull the Release

```bash
git fetch upstream --tags
git checkout -b sfops-v31.11.0-update
git merge v31.11.0
git push origin sfops-v31.11.0-update
```

Create and merge the PR via GitHub UI.

### Step 3: Copy Workflows to Your Project

Workflows have moved to `project-workflows/v1/`. Copy them to your project:

```bash
cp project-workflows/v1/*.yml .github/workflows/
```

{% hint style="warning" %}
The file `sfops-cron-ci-sandbox-create-v2.yml` has been renamed to `sfops-cron-ci-sandbox-create.yml`. Delete the old file from your `.github/workflows/` if it exists.
{% endhint %}

### Step 4: Update sfops-close-issue.yml (Action Required)

Open `.github/workflows/sfops-close-issue.yml` and add secrets for each environment you want cleaned up when issues are closed.

Add the relevant secrets to the `secrets:` section:

```yaml
secrets:
  DEVHUB_SFDX_AUTH_URL: ${{ secrets.DEVHUB_SFDX_AUTH_URL }}
  STAGING_SFDX_AUTH_URL: ${{ secrets.STAGING_SFDX_AUTH_URL }}
  PREPROD_SFDX_AUTH_URL: ${{ secrets.PREPROD_SFDX_AUTH_URL }}
  UAT_SFDX_AUTH_URL: ${{ secrets.UAT_SFDX_AUTH_URL }}
  QA_SFDX_AUTH_URL: ${{ secrets.QA_SFDX_AUTH_URL }}
  IQA_SFDX_AUTH_URL: ${{ secrets.IQA_SFDX_AUTH_URL }}
  SIT_SFDX_AUTH_URL: ${{ secrets.SIT_SFDX_AUTH_URL }}
  # ... keep existing secrets
```

Only include environments you actually use. These secrets must exist in your repository settings.

### Step 5: Configure AI Provider (Optional)

If you want AI-powered PR analysis, add one or both secrets to your repository:

| Secret | Provider |
|--------|----------|
| `COPILOT_TOKEN` | GitHub Copilot |
| `ANTHROPIC_API_KEY` | Anthropic Claude |

For GitHub Copilot setup, see: [Configuring LLM Providers - GitHub Copilot](https://docs.flxbl.io/flxbl/sfp/getting-started/configuring-llm-providers#github-copilot)

Then ensure the secret is passed in `.github/workflows/sfops-open-pr.yml`:

```yaml
secrets:
  # ... existing secrets
  COPILOT_TOKEN: ${{ secrets.COPILOT_TOKEN }}
  ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
```

### Step 6: Verify Release Workflows Have NPM_TOKEN

Check that `.github/workflows/sfops-open-release-issue.yml` and `.github/workflows/sfops-open-release-release-any-envs.yml` include:

```yaml
secrets:
  # ... existing secrets
  NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
```

---

## Updating v2 Project Workflows

### Step 1: Create a Backup Tag

Same as v1 - see Step 1 above.

### Step 2: Pull the Release

Same as v1 - see Step 2 above.

### Step 3: Copy Workflows to Your Project

```bash
cp project-workflows/v2/*.yml .github/workflows/
```

### Step 4: Configure AI Provider (Optional)

If you want AI-powered PR analysis, add secrets and update `.github/workflows/sfops-open-pr.yml` as described in v1 Step 5 above.

---

## Validation Checklist

### v1 Users

- [ ] Updated sfp-pro to 50.1.0 and rebuilt Docker images
- [ ] Created backup tag `pre-v31.11.0-backup`
- [ ] Pulled sfops v31.11.0 and merged to main
- [ ] Copied workflows from `project-workflows/v1/`
- [ ] Deleted old `sfops-cron-ci-sandbox-create-v2.yml` if it exists
- [ ] Added environment auth URL secrets for close-issue workflow
- [ ] Verified `NPM_TOKEN` in release workflows
- [ ] (Optional) Configured AI provider secrets
- [ ] Tested a PR to verify validation workflow runs

### v2 Users

- [ ] Updated sfp-pro to 50.1.0 and rebuilt Docker images
- [ ] Created backup tag `pre-v31.11.0-backup`
- [ ] Pulled sfops v31.11.0 and merged to main
- [ ] Copied workflows from `project-workflows/v2/`
- [ ] (Optional) Configured AI provider secrets
- [ ] Tested a PR to verify validation workflow runs

---

## Rollback

If you encounter issues, revert to your backup tag:

```bash
git checkout pre-v31.11.0-backup
git checkout -b rollback-v31.11.0
git push origin rollback-v31.11.0
```

Create and merge the PR to restore your previous state.
