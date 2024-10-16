---
description: >-
  For users who need to update to v29.0.0, featuring the revised issue ops
  engine
---

# Updating to v29.0.0

{% hint style="info" %}
This is not applicable for x-tend customers, as your flxbl point of contact would have upgraded your instance.
{% endhint %}

1. Follow normal update process for self managed instances. This release needs both docker updates as well as workflow updates.
2. Disable issues in the corresponding project repository in  GitHub, you will activate once the migration is complete. This will prevent issues from being created in the meantime
3. Delete all existing Issue Templates in your project repository. You might need to create a PR for the same
4. Uptate the following project workflows with below yaml's

**.github/workflows/sfops-close-issue.yml**

```
name: sfops - Close Issues

on:
  issues:
    types: [ closed ]



run-name: sfops - Close Issue-${{ github.event.issue.number }}

jobs:
 close-pending-action-issues:
    name: 'Close Pending Action On Issues'
    uses:   <your-org-name>/sfops-gh-actions/.github/workflows/close-pending-action-on-issues.yml@main
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

**.github/workflows/sfops-open-release-any-envs.yml**

```
name: 'sfops -  Execute Releases to any env'
run-name: 'Deploy a release to envs -${{ github.event.issue.number }}-requested by-${{ github.event.issue.user.login }}'
on:
  issues:
    types: [ opened , reopened]


jobs:
  execute-release-issue-any-envs:
    name: 'Act on sfops release issue to any env'
    uses:  <your-org-name>/sfops-gh-actions/.github/workflows/exec-release-to-any-env.yml@main
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

**.github/workflows/sfops-open-release-issue.yml**

```
name: 'sfops -  Execute Releases'
run-name: 'Deploy a release to release category envs -${{ github.event.issue.number }}-requested by-${{ github.event.issue.user.login }}'
on:
  issues:
    types: [ opened , reopened]


jobs:
 execute-release-issue-release-envs:
    name: 'Act on sfops release issue'
    uses:   <your-org-name>/sfops-gh-actions/.github/workflows/exec-release-to-release-envs.yml@main
    with:
      issue-number: ${{ github.event.issue.number}}
      issue-body: ${{ github.event.issue.body }}
      issue-title:  ${{ github.event.issue.title }} 
      sfopsbot-app-id: ${{ vars.SFOPSBOT_APP_ID }}
      source-repo-url: https://github.com/${{github.repository}}
      workitem-url:  ${{ vars.SFOPS_ISSUETRACKER_URL }}
      metrics-provider:  ${{ vars.SFOPS_METRICS_PROVIDER }}
      dashboard-repo: ${{ vars.SFOPS_DASHBOARD_REPO }}
      issue-notifier: ${{ github.repository }}
    secrets:
      DEVHUB_SFDX_AUTH_URL: ${{ secrets.DEVHUB_SFDX_AUTH_URL }}
      SB_SFDX_AUTH_URL: ${{ secrets.SB_SFDX_AUTH_URL }}
      DATADOG_API_KEY: ${{ secrets.DATADOG_API_KEY }}
      DATADOG_HOST: ${{ secrets.DATADOG_HOST }}
      SFOPSBOT_APP_PRIVATE_KEY: ${{ secrets.SFOPSBOT_APP_PRIVATE_KEY }}
      NPM_TOKEN: ${{ secrets.GITHUB_TOKEN }} 
```



5. Install [https://www.npmjs.com/package/@flxbl-io/sfops-migrator](https://www.npmjs.com/package/@flxbl-io/sfops-migrator), using npm i -g  @flxbl-io/sfops-migrator
6.  From a terminal run, ensure GITHUB\_ACCESS\_TOKEN has permissions to update and delete repository variables, and read and write issues

    ```
    sfops-migrator OWNER REPO YOUR_GITHUB_ACCESS_TOKEN
    ```
7. Renable Issues
8. Reach out to #sfops channel, if you have done the following changes and for us to enable roll out to your orgs (for flxbl managed instances)
