# sfops -  Execute Releases to any env

This workflow is triggered when a release-related issue is opened or reopened in the repository. It's designed to execute releases to any environment based on the issue content.

```
/name: 'sfops -  Execute Releases to any env'
run-name: 'Deploy a release to envs -${{ github.event.issue.number }}-requested by-${{ github.event.issue.user.login }}'
on:
  issues:
    types: [ opened , reopened]


jobs:
  execute-release-issue-any-envs:
    name: 'Act on sfops release issue to any env'
    uses:   flxbl-io/sfops-gh-actions/.github/workflows/exec-release-to-any-env.yml@main
    with:
      issue-number: ${{ github.event.issue.number}}
      issue-body: ${{ github.event.issue.body }}
      issue-title:  ${{ github.event.issue.title }} 
      metrics-provider:  ${{ vars.SFOPS_METRICS_PROVIDER }}
      sfopsbot-app-id: ${{ vars.SFOPSBOT_APP_ID }}
    secrets:
      DEVHUB_SFDX_AUTH_URL: ${{ secrets.DEVHUB_SFDX_AUTH_URL }}
      SB_SFDX_AUTH_URL: ${{ secrets.SB_SFDX_AUTH_URL }}
      DATADOG_API_KEY: ${{ secrets.DATADOG_API_KEY }}
      DATADOG_HOST: ${{ secrets.DATADOG_HOST }}
      SFOPSBOT_APP_PRIVATE_KEY: ${{ secrets.SFOPSBOT_APP_PRIVATE_KEY }}
      NPM_TOKEN: ${{ secrets.GITHUB_TOKEN }}    
```

Run Name:

* Dynamically set to 'Deploy a release to envs -\[issue number]-requested by-\[username]'
* This provides a clear, informative name for each workflow run, indicating it's a release deployment

Trigger:

* The workflow is activated when an issue is opened or reopened in the repository.

This workflow is designed to handle the execution of releases to any environment when a release-related issue is created or reopened. It passes relevant information about the issue to a reusable workflow, which  contains the logic for parsing the issue details and executing the release process.
