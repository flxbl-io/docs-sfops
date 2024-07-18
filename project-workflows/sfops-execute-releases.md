# sfops - Execute Releases

Here's a documentation for the provided YAML in a similar style:

This workflow is triggered when a release-related issue is opened or reopened in the repository. It's designed to execute releases specifically to environments categorized as release environments.

```
name: 'sfops -  Execute Releases'
run-name: 'Deploy a release to release category envs -${{ github.event.issue.number }}-requested by-${{ github.event.issue.user.login }}'
on:
  issues:
    types: [ opened , reopened]


jobs:
 execute-release-issue-release-envs:
    name: 'Act on sfops release issue'
    uses:   flxbl-io/sfops-gh-actions/.github/workflows/exec-release-to-release-envs.yml@main
    with:
      issue-number: ${{ github.event.issue.number}}
      issue-body: ${{ github.event.issue.body }}
      issue-title:  ${{ github.event.issue.title }} 
      sfopsbot-app-id: ${{ vars.SFOPSBOT_APP_ID }}
      source-repo-url: https://github.com/${{github.repository}}
      workitem-url:  ${{ vars.SFOPS_ISSUETRACKER_URL }}
      metrics-provider:  ${{ vars.SFOPS_METRICS_PROVIDER }}
      dashboard-repo: ${{ vars.SFOPS_DASHBOARD_REPO }}
      issue-notifier: 'flxbl-io/sf-core'
    secrets:
      DEVHUB_SFDX_AUTH_URL: ${{ secrets.DEVHUB_SFDX_AUTH_URL }}
      SB_SFDX_AUTH_URL: ${{ secrets.SB_SFDX_AUTH_URL }}
      DATADOG_API_KEY: ${{ secrets.DATADOG_API_KEY }}
      DATADOG_HOST: ${{ secrets.DATADOG_HOST }}
      SFOPSBOT_APP_PRIVATE_KEY: ${{ secrets.SFOPSBOT_APP_PRIVATE_KEY }}
      NPM_TOKEN: ${{ secrets.GITHUB_TOKEN }}   
```

Run Name:

* Dynamically set to 'Deploy a release to release category envs -\[issue number]-requested by-\[username]'
* This provides a clear, informative name for each workflow run, indicating it's a release deployment to specific environment categories

Trigger:

* The workflow is activated when an issue is opened or reopened in the repository.

This workflow is designed to handle the execution of releases specifically to environments categorized as release environments when a release-related issue is created or reopened. It passes relevant information about the issue and various configuration details to a reusable workflow, which  contains the logic for parsing the issue details and executing the release process.
