# May25

### 🏗️ Major Architecture Change: No More Rebuilding SFP!

Starting with v31.1.0, SFOPS fundamentally changed how Docker images are built. If you fork SFOPS, you no longer need to rebuild the entire sfp toolchain.

#### What Changed <a href="#what-changed" id="what-changed"></a>

**Before v31.1.0:**

* Users had to rebuild the entire sfp toolchain when forking SFOPS
* Complex dependency management across multiple tools
* Maintenance burden of keeping sfp versions in sync
* Risk of version conflicts and build failures

**After v31.1.0:**

* SFOPS now uses **pre-built sfp base images**
* `sfops` extends `sfp-pro` base image (just adds GitHub CLI)
* `sfops-lite` extends `sfp-pro-lite` base image
* No more rebuilding sfp - just use the official images
* Clean separation: sfp team maintains core tools, SFOPS adds orchestration

**New in v31.1.1:**

* Base image versions are now configurable via GitHub variables
* No more hardcoded "latest" tags - you control the versions

### 🚨 Action Required for Forked Repositories <a href="#f0-9f-9a-a8-action-required-for-forked-repositories" id="f0-9f-9a-a8-action-required-for-forked-repositories"></a>

If you maintain a fork of SFOPS and build your own Docker images, you **must** update your GitHub configuration:

### 1. Configure GitHub Variables <a href="#id-1-configure-github-variables" id="id-1-configure-github-variables"></a>

In your GitHub fork, navigate to **Settings** → **Secrets and variables** → **Actions** → **Variables**

Add these repository variables:

| Variable Name    | Required Value    | Description                               |
| ---------------- | ----------------- | ----------------------------------------- |
| `BASE_REGISTRY`  | `source.flxbl.io` | Registry where sfp base images are hosted |
| `BASE_REPO`      | `sfops`           | Repository namespace                      |
| `BASE_IMAGE_TAG` | `v31.1.0`         | Specific version (don't use`latest`)      |

### 2. Configure Registry Credentials <a href="#id-2-configure-registry-credentials" id="id-2-configure-registry-credentials"></a>

The sfp base images are hosted on source.flxbl.io. You need to configure credentials in GitHub:

1. In your GitHub fork, go to **Settings** → **Secrets and variables** → **Actions** → **Secrets**
2. Add these secrets:
   * `BASE_REGISTRY_USERNAME`: Your source.flxbl.io username
   * `BASE_REGISTRY_TOKEN`: Your source.flxbl.io access token

To get your source.flxbl.io token:

* Log into source.flxbl.io → Settings → Applications → Generate New Token
* Grant package registry read permissions
* Copy the token to use in GitHub

### 3. Update Project Workflows (Important!) <a href="#id-3-update-project-workflows-important" id="id-3-update-project-workflows-important"></a>

If your Salesforce projects use SFOPS workflows, you need to update them:

**What Changed**

* The `sfops-push-to-branch.yml` workflow was removed (branch-deploy deprecated)
* Several reusable workflows were updated to remove branch-deploy dependencies

**How to Update**

1. Navigate to your Salesforce project repository (not your SFOPS fork)
2. Check `.github/workflows/` for these SFOPS workflows:
   * `sfops-push-to-branch.yml` - DELETE this file
   * Other `sfops-*.yml` files - UPDATE to latest versions
3. Copy the latest workflow files from `sfops-gh-actions/project-workflows/`
4. Commit the updated workflows to your project

**Note:** This is separate from updating your SFOPS fork - this is about updating the workflows in your actual Salesforce projects that consume SFOPS.

### 4. Build Your Own Base Images (Optional) <a href="#id-4-build-your-own-base-images-optional" id="id-4-build-your-own-base-images-optional"></a>

If you need custom base images:

1. Fork the sfp-pro repository
2. Build and publish your custom sfp-pro images
3. Update your SFOPS fork to use your custom base images

### 💡 Benefits of the New Architecture <a href="#f0-9f-92-a1-benefits-of-the-new-architecture" id="f0-9f-92-a1-benefits-of-the-new-architecture"></a>

#### Faster CI/CD <a href="#faster-cicd" id="faster-cicd"></a>

* Docker builds are 3-5x faster
* Base layers are cached and reused
* Less bandwidth usage

#### Better Version Control <a href="#better-version-control" id="better-version-control"></a>

* Pin specific base image versions for stability
* Test new versions before rolling out
* Consistent builds across teams

#### Easier Maintenance <a href="#easier-maintenance" id="easier-maintenance"></a>

* No more rebuilding sfp when you just need SFOPS changes
* sfp updates are handled by the Flxbl team
* Your fork only needs to track SFOPS-specific customizations
* Cleaner separation between tooling (sfp) and orchestration (SFOPS)

### 📋 Quick Migration Checklist <a href="#f0-9f-93-8b-quick-migration-checklist" id="f0-9f-93-8b-quick-migration-checklist"></a>

#### For Your SFOPS Fork <a href="#for-your-sfops-fork" id="for-your-sfops-fork"></a>

1. **Get latest code**
   * [ ] &#x20;Pull latest SFOPS code from source.flxbl.io
   * [ ] &#x20;Push to your GitHub fork
2. **Configure GitHub Variables** (in your SFOPS fork)
   * [ ] &#x20;Set `BASE_REGISTRY` to `source.flxbl.io`
   * [ ] &#x20;Set `BASE_REPO` to `sfops`
   * [ ] &#x20;Set `BASE_IMAGE_TAG` to `v31.1.0` (not `latest`)
3. **Configure GitHub Secrets** (in your SFOPS fork)
   * [ ] &#x20;Create access token at source.flxbl.io
   * [ ] &#x20;Add `BASE_REGISTRY_USERNAME` secret
   * [ ] &#x20;Add `BASE_REGISTRY_TOKEN` secret
4. **Test Docker Build**
   * [ ] &#x20;Push a change to trigger build
   * [ ] &#x20;Verify build succeeds

#### For Your Salesforce Projects <a href="#for-your-salesforce-projects" id="for-your-salesforce-projects"></a>

1. **Update project workflows**
   * [ ] &#x20;Delete `.github/workflows/sfops-push-to-branch.yml`
   * [ ] &#x20;Update other `sfops-*.yml` workflows from latest sfops-gh-actions
   * [ ] &#x20;Commit and push changes
2. **Test updated workflows**
   * [ ] &#x20;Create a PR to test workflows
   * [ ] &#x20;Verify all automations work correctly

### 🆕 Other Functional Changes (v31.0.3 → v31.1.1) <a href="#f0-9f-86-95-other-functional-changes-v3103-e2-86-92-v3111" id="f0-9f-86-95-other-functional-changes-v3103-e2-86-92-v3111"></a>

#### DevCentral Dashboard Enhancement <a href="#devcentral-dashboard-enhancement" id="devcentral-dashboard-enhancement"></a>

* **SFP Server Integration**: DevCentral dashboards now support SFP server-based front door URL functionality
* Review environments, sandboxes, and scratch orgs pages updated to work with SFP server
* Improved URL handling for better environment access

#### Major PR Validation Enhancement <a href="#major-pr-validation-enhancement" id="major-pr-validation-enhancement"></a>

* **GitHub Checks API Integration**: PR validation results are now reported through GitHub's native Checks API instead of comments
* No more cluttered PR comments with validation results
* Cleaner PR interface with validation status shown in GitHub's checks panel
* Better integration with GitHub's merge requirements and branch protection rules

#### Workflow Improvements <a href="#workflow-improvements" id="workflow-improvements"></a>

* **SFP Server Support**: All workflows now pass SFP server URL and token through the pipeline
* **New Release Configuration**: Added "thorough" as a release configuration option
* **Better Variable Management**: Fixed incorrect variables being passed between workflows
* Environment variables now properly propagate across all job stages

#### Removed Features <a href="#removed-features" id="removed-features"></a>

* **Deprecated Actions**: Removed unused actions (checkActions, create-or-update-project-card)
* **fetchAllReleaseNames**: Moved to sfp-pro for better maintainability
* **Branch Deploy**: Completely removed from all workflows (see project workflow updates above)

#### Bug Fixes <a href="#bug-fixes" id="bug-fixes"></a>

* Fixed filename changes for sfp-pro compatibility
* Corrected variable passing in project workflows
* Improved environment variable handling across jobs

### 🔄 Version Compatibility <a href="#f0-9f-94-84-version-compatibility" id="f0-9f-94-84-version-compatibility"></a>

* SFOPS v31.1.x requires sfp-pro base images v31.0.0 or later
* Using mismatched versions may cause unexpected behavior
* Always test with your specific base image versions

### 🔍 Troubleshooting <a href="#f0-9f-94-8d-troubleshooting" id="f0-9f-94-8d-troubleshooting"></a>

#### Common Issues <a href="#common-issues" id="common-issues"></a>

**Build fails with "manifest unknown" error**

* Check that your `BASE_REGISTRY` and `BASE_REPO` values are correct
* Verify the base image exists at the specified location
* Ensure your access token has read permissions

**Authentication errors**

* Ensure your source.flxbl.io token has package read permissions
* Token might be expired - generate a new one at source.flxbl.io
* Username must match your source.flxbl.io login username
* The token must be configured in your GitHub fork's secrets

**Wrong base image version**

* Clear your Docker cache: `docker system prune -a`
* Explicitly set `BASE_IMAGE_TAG` instead of using `latest`

### 📚 Further Reading <a href="#f0-9f-93-9a-further-reading" id="f0-9f-93-9a-further-reading"></a>

* [Source Repository](https://source.flxbl.io/sfops/sfops)
* [Docker Build Workflow](https://file+.vscode-resource.vscode-cdn.net/Users/azlam/projects/sfops/.github/workflows/trigger-docker.yml)
* [Base Image Configuration Documentation](https://docs.flxbl.io/sfops)

***

**Need help?** Reach out to your dedicated support channel in Flxbl's Slack workspace.

**Security concerns?** Audit the sfp source code at source.flxbl.io to understand what's included in the base images.
