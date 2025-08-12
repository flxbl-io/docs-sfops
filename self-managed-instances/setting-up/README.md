# Setup for self managed instances

The below diagram describes how updates are rolled out to a customer's GitHub org when a customer prefers a self managed instance

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption><p>Conceptual Model for sfops when self managed</p></figcaption></figure>

## Quick Start Guide

This guide walks you through setting up a self-managed instance of sfops in your GitHub organization, allowing you to maintain full control over your CI/CD pipelines while receiving updates from the upstream sfops repository.

### Prerequisites

1. **Gitea Access Token**
   - Log into [source.flxbl.io](https://source.flxbl.io) (contact flxbl team for access)
   - Go to [User Settings → Applications](https://source.flxbl.io/user/settings/applications)
   - Click "Generate New Token" under Manage Access Tokens
   - Configure the token:
     - Token Name: e.g., "sfops-access"
     - Repository and Organization Access: **All (public, private, and limited)**
     - Permissions:
       - `repository`: **Read**
       - `package`: **Read**

2. **GitHub Personal Access Token (Classic)** for `GHA_TOKEN`
   
   The built-in `GITHUB_TOKEN` cannot trigger workflows in other repositories or push packages. Create a PAT for:
   - Deploying workflows to `sfops-gh-actions` and `sfops-dev-central` repositories  
   - Publishing Docker images to GitHub Container Registry (ghcr.io)
   - Creating pull requests for upstream synchronization
   
   **Steps:**
   - Navigate to: GitHub → Settings → Developer settings → Personal access tokens → Tokens (classic)
     - Direct URL: `https://github.com/settings/tokens`
   - Click "Generate new token" → "Generate new token (classic)"
   - Add a descriptive note: e.g., "sfops GHA_TOKEN"
   - Set expiration based on your security requirements
   - Select the following scopes:
     - ☑️ **repo** (Full control of private repositories)
     - ☑️ **workflow** (Update GitHub Action workflows)
     - ☑️ **write:packages** (Upload packages to GitHub Package Registry)
     - ☑️ **read:org** (under admin:org section - Read org and team membership)
   - Click "Generate token" at the bottom of the page
   - **⚠️ IMPORTANT:** Copy the token immediately! GitHub only shows it once
   - **Keep this browser tab open** until you've configured the token as `GHA_TOKEN` in Step 4

### Step-by-Step Setup Process

#### Step 1: Fork sfops from Gitea to GitHub

**Option A: GitHub Import (Recommended)**

Use GitHub's import feature for a quick setup:

1. Navigate to: **New repository → Import a repository**
   - Direct URL: `https://github.com/new/import`
2. Enter repository URL: `https://source.flxbl.io/flxbl/sfops.git`
3. For authentication:
   - Username: Your Gitea username
   - Password: Your Gitea token (created above)
4. Name your repository: `sfops`
5. Set visibility to **Internal**
6. Click "Begin import"

For future updates from upstream:
- **Automated**: Configure the [Sync Upstream Repository workflow](../workflow-details/sync-upstream-repository-and-create-pull-request.md) - requires `GHA_TOKEN` (your GitHub PAT from prerequisites) and `SFOPS_UPSTREAM_URL` secrets configured in Step 4
- **Manual**: See Option B below for command-line syncing

**Option B: Manual Clone & Push**

For manual control over the import process:

```bash
# Clone the sfops repository from Gitea
git clone https://<username>:<gitea-token>@source.flxbl.io/flxbl/sfops.git sfops
cd sfops

# Set your GitHub repository as origin
git remote remove origin
git remote add origin https://github.com/<your-org>/sfops.git

# Push to your GitHub repository
git push -u origin --all
git push -u origin --tags

# Add upstream remote for manual syncing
git remote add upstream https://<username>:<gitea-token>@source.flxbl.io/flxbl/sfops.git
```

#### Step 2: Create Required Repositories

Ensure the following repositories exist in your GitHub organization:
- `sfops` - Source code (already created in Step 1)
- `sfops-gh-actions` - Create as empty repository:
  - Visibility: **Internal**
  - Do NOT add README, .gitignore, or license
  - Will be populated by sfops workflows
- `sfops-dev-central` - Create as empty repository:
  - Visibility: **Internal**
  - Do NOT add README, .gitignore, or license
  - Will be populated by sfops workflows
- Your existing Salesforce project repository (will be configured to use sfops)

[→ Detailed repository setup](./1.-create-repositories.md)

#### Step 3: Configure GitHub App

Create a GitHub App (`sfops-bot`) for authentication and enhanced permissions:
- Navigate to: **GitHub org → Settings → Developer settings → GitHub Apps → New GitHub App**
  - Direct URL: `https://github.com/organizations/YOUR_ORG/settings/apps/new`
- Name: `sfops-bot`
- Configure permissions (see detailed guide for full list - includes Contents, Actions, Workflows, etc.)
- Generate and save the private key (`.pem` file)
- Note the App ID for later use

[→ Detailed GitHub App configuration](./github-app.md)

#### Step 4: Configure Secrets and Variables

Add required secrets and variables to your sfops repository for Docker image building, deployment, and upstream synchronization.

[→ Repository configuration](./3.-setting-up-sfops-repository.md)

#### Step 5: Trigger Initial Workflows

Build and deploy your sfops instance to the target repositories.

[→ Workflow execution](./4.-trigger-the-workflows.md)

#### Step 6: Set Up Your Salesforce Project

Configure your Salesforce project repository to use sfops workflows.

[→ Project setup](./5.-setting-up-project-repository.md)

#### Step 7: Configure Update Process

Set up automated synchronization with upstream sfops releases.

[→ Update configuration](./6.-fetching-upstream-changes/README.md)

### Support

For assistance with setup:
- Contact your sfops point of contact
- Check the #sfops Slack channel
- Review the [troubleshooting guide](../update-instructions/README.md)
