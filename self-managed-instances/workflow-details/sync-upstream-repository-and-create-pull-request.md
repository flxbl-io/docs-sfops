# Sync Upstream Repository and Create Pull Request

This script and its associated workflow automates syncing a specific tag from an upstream repository (source .flxbl.io) to a downstream repository (your sfops directory in your GitHub Organisation)  and creating a pull request with the changes.&#x20;

### Prerequisites

Before using this script, ensure that the following prerequisites are met:

* The necessary secrets are set up in the GitHub repository's secrets:
  * `GHA_TOKEN`: A personal access token with the required permissions to clone the repository and create a pull request.
  * `SFOPS_UPSTREAM_URL`: The URL of the upstream repository from which the specific tag will be synced.

### Workflow Trigger

The workflow is triggered manually using the `workflow_dispatch` event. When triggering the workflow, you need to provide the following input:

* `tag`: The specific tag from the upstream repository that you want to sync.

### Functionality

When the workflow is triggered, the script performs the following steps:

1. Clones the downstream repository into a temporary directory.
2. Sets up the GitHub bot user for committing changes.
3. Adds the upstream repository as a remote.
4. Fetches the tags from the upstream repository.
5. Checks if the specified tag exists in the upstream repository.
   * If the tag exists:
     * Creates a new branch from the main branch of the downstream repository.
     * Merges the specific tag from the upstream repository into the new branch, favoring changes from the upstream tag in case of conflicts.
     * Checks if there are any changes to `version.txt` and `CHANGELOG.md` compared to the main branch. If changes are detected, replaces these files with the versions from the main branch and commits the changes.
     * Pushes the new branch to the downstream repository.
     * Creates a pull request with the changes, using the GitHub CLI.
   * If the tag doesn't exist:
     * Skips the branch creation and pull request creation.
6. Cleans up the temporary directory.

If any command in the script fails, the script will exit immediately, and the workflow will be marked as failed, providing notifications to the user.

### Required Secrets

To run this script successfully, the following secrets need to be set up in the GitHub repository's secrets:

* `GHA_TOKEN`: A personal access token with the necessary permissions to clone the repository and create a pull request. You can create a personal access token in your GitHub account settings.
* `SFOPS_UPSTREAM_URL`: The URL of the upstream repository from which the specific tag will be synced. This should be in the format [https://\<username>:\<token>@source.flxbl.io/flxbl/sfops.git](https://source.flxbl.io/flxbl/sfops.git).

You can navigate to [https://source.flxbl.io/user/settings/applications](https://source.flxbl.io/user/settings/applications) to generate a read only token to fetch from upstream

Make sure to set these secrets in your repository's settings before running the workflow.

### Troubleshooting

If the workflow fails, check the logs for detailed error messages. Common issues could include:

* Invalid or missing secrets.
* Insufficient permissions for the `GHA_TOKEN`. Select at least the following permissions:
  * repo (full control)
  * workflow
* Network connectivity issues while cloning the repository or fetching tags.
* Conflicts during the merge process that could not be resolved automatically.
* If you get `remote: Repository not found.` or `fatal: repository '***/' not found`, you're likely setting the `SFOPS_UPSTREAM_URL` incorrectly. Make sure it follows the format specified above and try it locally on your machine as well using `git fetch https://<username>:<token>@source.flxbl.io/flxbl/sfops.git`

If you encounter any issues, review the logs and ensure that the prerequisites and secrets are properly set up.
