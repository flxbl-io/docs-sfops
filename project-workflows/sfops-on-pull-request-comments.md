# sfops - On Pull Request Comments

This workflow gets triggered on a comment created in an issue within the project repository. &#x20;

```
name: "sfops - On Pull Request Comments"

on:
  issue_comment:
    types: [created]

jobs:
  exec-on-pr-comment:
    name: "Execute Action on PR Comment"
    uses: flxbl-io/sfops-gh-actions/.github/workflows/exec-on-pr-comment.yml@main
    with:
      issue-number: ${{ github.event.issue.number }}
      pathToReleaseConfigs: "config"
      dashboard-repo: ${{ vars.SFOPS_DASHBOARD_REPO }}
      releasename: ${{ vars.releaseName }}
      metrics-provider: ${{ vars.SFOPS_METRICS_PROVIDER }}
      workitem-url: ${{ vars.SFOPS_ISSUETRACKER_URL }}
      workitem-filter: ${{ vars.SFOPS_ISSUETRACKER_WORKITEM_FILTER }}
      sfopsbot-app-id: ${{ vars.SFOPSBOT_APP_ID }}
    secrets:
      DEVHUB_SFDX_AUTH_URL: ${{ secrets.DEVHUB_SFDX_AUTH_URL }}
      SB_SFDX_AUTH_URL: ${{ secrets.SB_SFDX_AUTH_URL }}
      DATADOG_API_KEY: ${{ secrets.DATADOG_API_KEY }}
      DATADOG_HOST: ${{ secrets.DATADOG_HOST }}
      SFOPSBOT_APP_PRIVATE_KEY: ${{ secrets.SFOPSBOT_APP_PRIVATE_KEY }}
      NPM_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```



The reusable workflow expands into multiple jobs and is responsible for the following actions

* **Extend a review sandbox:**  If you want to extend a review sandbox for more than the default duration of 24 hours, this workflow triggers the action responsible for extending based on the comment `sfops extend` issued in the PR comments
* **Renew a review sandbox:**  Fetch a new review sandbox to be associated with your PR, if a comment with `sfops renew` is created
* **Retrigger validation with new flags:** Change validation behavior by adding validation control commands (`sfops fast`, `sfops individual`, `sfops quick`, `sfops skiptests`) in a PR comment. The PR will automatically close and reopen to apply the new flags
* **Deploy a PR to test environment:** If you have activated the branch deploy model, you will be able to deploy the PR to a test environment before merging when a comment with `sfops test` is created

## Available Commands

| Command | Description |
|---------|-------------|
| `sfops extend` or `/sfops extend` | Extend the review sandbox duration beyond 24 hours |
| `sfops renew` or `/sfops renew` | Fetch a new review sandbox for the PR |
| `sfops fast` or `/sfops fast` | Re-run validation in individual mode (faster, diff-based) |
| `sfops individual` or `/sfops individual` | Re-run validation in individual mode |
| `sfops quick` or `/sfops quick` | Re-run validation in individual mode |
| `sfops skiptests` or `/sfops skiptests` | Re-run validation with tests skipped |
| `sfops test` or `/sfops test` | Deploy the PR to a test environment (branch deploy model) |

{% hint style="info" %}
The `/` prefix is optional for all commands!
{% endhint %}

For more details on validation control commands, see [PR Validation Controls](../environments/review-environments/pr-validation-controls.md).
