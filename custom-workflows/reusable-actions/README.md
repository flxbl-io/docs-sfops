---
description: Reusable GitHub Actions for building custom sfops workflows
---

# Reusable Actions

Reusable actions are building blocks provided by sfops that you can use to create custom workflows. These composite actions encapsulate common functionality and can be used in any workflow within your sfops repository.

## Available Actions

### Token & Authentication Actions

* [**Get GitHub Token**](get-github-token.md) - Fetch GitHub tokens from sfp server for repository operations without configuring GitHub Apps
* [**Hybrid Environment Authentication**](auth-to-environment-hybrid.md) - Intelligent authentication that automatically uses SFP server or native SFDX authentication based on environment registration
* [**Hybrid DevHub Authentication**](auth-to-devhub-hybrid.md) - Flexible DevHub authentication with automatic fallback between SFP server and native methods
* [**Authentication with Lock**](auth-to-environment-with-lock.md) - Secure authentication with exclusive environment locking for critical operations
* [**Authentication without Lock**](auth-to-environment-without-lock.md) - Streamlined authentication for read-only operations and concurrent access

### Environment Discovery Actions

* [**Fetch All Environments**](fetch-all-envs.md) - Dynamically fetch and filter environments from GitHub Environments or sfp server for matrix builds and multi-environment workflows
* [**Match Pool**](match-pool.md) - Match pool assignment rules based on branch and domain patterns for intelligent environment selection

### Change Detection Actions

* [**Impacted Release Definition Detector**](impacted-release-defn-detector.md) - Detect which release definitions are impacted by code changes for targeted validation and deployment

### PR Interaction Actions

* [**Add PR Comment**](add-pr-comment.md) - Add, update, and manage PR comments with support for templating, status-based messages, and comment deduplication

### Other Actions

sfops includes many other reusable actions for various purposes. You can find them in the `/actions` directory of your sfops repository:

* `manageEnvDeploymentLabel` - Manage environment deployment labels on PRs
* `findCurrentPr` - Find the current pull request context
* `get-github-token` - Get GitHub App token for authentication
* `unlockEnvironment` - Release environment locks
* `updateGitRepo` - Update dashboard repository with workflow results
* And many more...

## Using Reusable Actions

All reusable actions in sfops are located in your sfops repository's `actions` directory. To use them in your custom workflow, reference them using the sfops template variables:

```yaml
jobs:
  your-job:
    runs-on: ubuntu-latest
    container: ${{ sfops.sfops_docker_image }}

    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Use a Reusable Action
        uses: ${{ sfops.repo_owner }}/${{ sfops.action_repository }}/[action-name]@main
        with:
          # Action inputs
          input1: value1
          input2: value2
```

The `${{ sfops.repo_owner }}/${{ sfops.action_repository }}` template variables automatically resolve to your organization and sfops repository (e.g., `your-org/sfops-gh-actions`).

## Creating Custom Workflows

Reusable actions allow you to build sophisticated custom workflows by combining multiple actions. Common use cases include:

* **Data operations** - Export/import data between environments
* **Maintenance automation** - Scheduled tasks and cleanup operations
* **Multi-environment operations** - Parallel operations across environments
