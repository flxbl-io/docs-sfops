# sfops - Close Issues

This workflow is triggered when an issue is closed in the repository. It's designed to handle any pending actions associated with the closed issue.

```

name: sfops - Close Issues
on:
  issues:
    types: [ closed ]



run-name: sfops - Close Issue-${{ github.event.issue.number }}

jobs:
 close-pending-action-issues:
    name: 'Close Pending Action On Issues'
    uses:   flxbl-io/sfops-gh-actions/.github/workflows/close-pending-action-on-issues.yml@main
    with:
      issue-number: ${{ github.event.issue.number}}
      issue-body: ${{ github.event.issue.body }}
      issue-title:  ${{ github.event.issue.title }} 
      sfopsbot-app-id: ${{ vars.SFOPSBOT_APP_ID }}
    secrets:
      DEVHUB_SFDX_AUTH_URL: ${{ secrets.DEVHUB_SFDX_AUTH_URL }}
      SB_SFDX_AUTH_URL: ${{ secrets.SB_SFDX_AUTH_URL }}
      DATADOG_API_KEY: ${{ secrets.DATADOG_API_KEY }}
      DATADOG_HOST: ${{ secrets.DATADOG_HOST }}
      SFOPSBOT_APP_PRIVATE_KEY: ${{ secrets.SFOPSBOT_APP_PRIVATE_KEY }}
      NPM_TOKEN: ${{ secrets.GITHUB_TOKEN }}    

```



This workflow is designed to handle any necessary actions or clean-up processes when an issue is closed. It passes relevant information about the closed issue to a reusable workflow, which likely manages the specifics of closing pending actions. The workflow also includes authentication for Salesforce environments and integration with monitoring tools like Datadog, suggesting it may update external systems or perform actions in Salesforce as part of the issue closure process.
