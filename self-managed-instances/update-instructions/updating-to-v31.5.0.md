# sfops v31.5.0 Migration Guide

## 📋 Required Migration Steps

### 1. Pull Latest sfops Release from Upstream
Sync your fork with the official sfops v31.5.0 release:

```bash
# Add upstream repository (one-time setup)
git remote add upstream https://source.flxbl.io/flxbl/sfops

# Fetch and create PR for v31.5.0
git fetch upstream --tags
git checkout -b sfops-v31.5.0-update
git merge v31.5.0
git push origin sfops-v31.5.0-update

# Create PR via GitHub UI and merge it to main
```

### 2. Configure NPM Settings

#### Add NPM_SCOPE Variable (Optional)
**Note:** Only required if you want to use a custom NPM scope different from your GitHub organization name. If not set, it defaults to your GitHub org name, which is usually fine.

If you need a custom scope:
- Go to Settings → Secrets and variables → Actions → Variables
- Add new repository variable: `NPM_SCOPE` 
- Value: your custom npm scope (e.g., `@customscope`)

#### Add NPM_RC Secret (Only for Non-GitHub Registries)
**Skip this if using GitHub Packages (default).** Only needed for JFrog, GitLab, Azure Artifacts, etc.
- Go to Settings → Secrets and variables → Actions → Secrets
- Add new repository secret: `NPM_RC`
- Value should contain your registry configuration (see Step 4 for details)

### 3. Update Workflow Files
Update the following workflow files to include the `npm-scope` input:
- `sfops-cron-env-operations-prepare-scratch-ci-pool.yml`
- `sfops-cron-env-operations-prepare-scratch-dev-pool.yml`
- `sfops-open-issue.yml`
- `sfops-open-release-any-envs.yml`
- `sfops-open-release-issue.yml`
- `sfops-push-to-branch.yml`

Add the `npm-scope` parameter to the `with` section and ensure `NPM_RC` is in `secrets`:

```yaml
jobs:
  build-test-publish:
    name: 'Build Test & Publish'
    uses: nisa/sfops-gh-actions/.github/workflows/push-to-branch.yml@main
    with:
      # ... existing inputs ...
      npm-scope: ${{ vars.NPM_SCOPE }}  # Add this line
    secrets:
      # ... existing secrets ...
      NPM_RC: ${{ secrets.NPM_RC }}  # Add this line if not already present
```

### 3. For Custom NPM Registry
If you want to use a non-GitHub NPM registry (JFrog, GitLab, Azure Artifacts, etc.):

#### Add NPM_RC Secret
1. Go to Settings → Secrets and variables → Actions → Secrets
2. Add new repository secret: `NPM_RC`
3. Value should contain your registry configuration:

```
@yourscope:registry=https://your-registry-url/
//your-registry-url/:_authToken=${NPM_TOKEN}
```

#### Verify Scope in NPM_RC
Ensure your `NPM_RC` secret includes the scope configuration that matches your `NPM_SCOPE` variable.

### 4. For GitHub Packages Users (Default)
If you're using GitHub Packages (the default), you only need to:
- Add the `NPM_SCOPE` variable (Step 1)
- Update workflow files (Step 2)
- No additional configuration needed

## ⚠️ Breaking Change Warning
The setup-npm-registry action now validates that custom npmrc configurations include the required scope. Workflows will fail if:
- `NPM_SCOPE` variable is missing
- Custom `NPM_RC` secret doesn't include the matching scope configuration

## 🔄 Rollback Plan (if necessary)
Pin workflows to previous version: `uses: flxbl-io/sfops/.github/workflows/push-to-branch.yml@v31.3.0`

## ✅ Validation Checklist
- [ ] Pulled sfops v31.5.0 from upstream
  - [ ] Created PR from `sfops-v31.5.0-update` branch to main
  - [ ] Merged the PR to main
  - [ ] Merged release-please PR that was created after the merge
  - [ ] Pushed changes to test org
- [ ] Added `NPM_RC` secret with correct scope
- [ ] Added `NPM_SCOPE` repository variable
- [ ] Updated all workflow files with `npm-scope` input
- [ ] Tested `sfops-push-to-branch.yml` workflow to verify full release flow works
- [ ] Verified packages are published to correct registry with correct scope