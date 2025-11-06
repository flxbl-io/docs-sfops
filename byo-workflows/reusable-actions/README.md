---
keywords:
  - reusable actions
  - custom workflows
  - GitHub actions
  - authentication actions
  - workflow building blocks
  - composite actions
  - custom automation
description: Reusable GitHub Actions for building custom sfops workflows
---

# Reusable Actions

Reusable actions are building blocks provided by sfops that you can use to create custom workflows. These composite actions encapsulate common functionality and can be used in any workflow within your sfops repository.

## Available Actions

### Authentication Actions

* [**Hybrid Environment Authentication**](auth-to-environment-hybrid.md) - Intelligent authentication that automatically uses SFP server or native SFDX authentication based on environment registration
* [**Hybrid DevHub Authentication**](auth-to-devhub-hybrid.md) - Flexible DevHub authentication with automatic fallback between SFP server and native methods
* [**Authentication with Lock**](auth-to-environment-with-lock.md) - Secure authentication with exclusive environment locking for critical operations
* [**Authentication without Lock**](auth-to-environment-without-lock.md) - Streamlined authentication for read-only operations and concurrent access

### Other Actions

sfops includes many other reusable actions for various purposes. You can find them in the `/actions` directory of your sfops repository:

* `authToEnvironment` - Standard environment authentication
* `authToEnvironmentWithLock` - Authentication with environment locking
* `authToEnvironmentWithoutLock` - Authentication without locking
* `manageEnvDeploymentLabel` - Manage environment deployment labels
* `comment` - Add comments to pull requests or issues
* `findCurrentPr` - Find the current pull request
* `get-github-token` - Get GitHub token for authentication
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

## Getting Help

For detailed documentation on specific actions:

* Check the action's `README.md` file in the `/actions/[action-name]` directory
* Review the `action.yml` file for input/output specifications
* Look at existing sfops workflows for usage examples

For more information on creating workflows, see the [Project Workflows](../../project-workflows/) section.
