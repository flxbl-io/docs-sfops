# sfops - Execute Issue Ops

Here's a documentation for the provided YAML in a similar style:

This workflow is triggered when an issue is opened or reopened in the repository. It's designed to analyze and act on sfops (Salesforce Operations) issues.

```
name: 'sfops - Execute Issue Ops'
run-name: '${{ github.event.issue.title }}-${{ github.event.issue.number }}-requested by-${{ github.event.issue.user.login }}'
on:
  issues:
    types: [ opened, reopened ]


jobs:
      
 analyze-issues:
    name: 'Act on sfops issues'
    uses:   flxbl-io/sfops-gh-actions/.github/workflows/issue-analyzer.yml@main
    with:
      issue-number: ${{ github.event.issue.number}}
      issue-body: ${{ github.event.issue.body }}
      issue-title:  ${{ github.event.issue.title }} 
      metrics-provider:  ${{ vars.SFOPS_METRICS_PROVIDER }}
      sfopsbot-app-id: ${{ vars.SFOPSBOT_APP_ID }}
      dashboard-repo: ${{ vars.SFOPS_DASHBOARD_REPO }}
    secrets:
      DEVHUB_SFDX_AUTH_URL: ${{ secrets.DEVHUB_SFDX_AUTH_URL }}
      SB_SFDX_AUTH_URL: ${{ secrets.SB_SFDX_AUTH_URL }}
      DATADOG_API_KEY: ${{ secrets.DATADOG_API_KEY }}
      DATADOG_HOST: ${{ secrets.DATADOG_HOST }}
      SFOPSBOT_APP_PRIVATE_KEY: ${{ secrets.SFOPSBOT_APP_PRIVATE_KEY }}
      NPM_TOKEN: ${{ secrets.GITHUB_TOKEN }}    
```

Run Name:

* Dynamically set to '\[issue title]-\[issue number]-requested by-\[username]'
* This provides a clear, informative name for each workflow run

Trigger:

* The workflow is activated when an issue is opened or reopened in the repository.

This workflow is designed to analyze and act on sfops issues as soon as they are opened or reopened. It passes relevant information about the issue to a reusable workflow, which likely contains the logic for analyzing the issue and determining appropriate actions.

The workflow integrates with various systems:

* Salesforce environments (DevHub and Sandbox) for potential automated actions or checks
* Datadog for possible metrics and monitoring
* A dashboard repository, possibly for updating status or metrics
* An sfopsbot, which may be used for automated responses or actions

This setup allows for a standardized, automated response to new or reopened issues, potentially streamlining the sfops process and ensuring consistent handling of issues across the project.
