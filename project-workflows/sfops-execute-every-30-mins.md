# sfops - Execute Every 30 mins

This workflow is designed to execute cron operations every 30 minutes. It can be triggered manually or runs on a schedule.\


```
name: 'sfops - Execute Every 30 mins'

# Read name of sandbox
on:
  workflow_dispatch:
  schedule:
      - cron: '30 * * * *'   


jobs:
  cron-every-30-mins:
    name: 'Exec cron operations every 30 mins'
    uses:   ${{ sfops.repo_owner }}/${{ sfops.action_repository }}/.github/workflows/cron-jobs-30-mins.yml@main
    with:
      sfopsbot-app-id: ${{ vars.SFOPSBOT_APP_ID }}
      path-to-config : "config/sandbox-pool-config.json"
      metrics-provider: ${{ vars.SFOPS_METRICS_PROVIDER }}
      dashboard-repo:  ${{ vars.SFOPS_DASHBOARD_REPO }}
    secrets:
      DEVHUB_SFDX_AUTH_URL: ${{ secrets.DEVHUB_SFDX_AUTH_URL }}
      DATADOG_API_KEY: ${{ secrets.DATADOG_API_KEY }}
      DATADOG_HOST: ${{ secrets.DATADOG_HOST }}
      SFOPSBOT_APP_PRIVATE_KEY: ${{ secrets.SFOPSBOT_APP_PRIVATE_KEY }}
      NPM_TOKEN: ${{ secrets.GITHUB_TOKEN }}    

```

### Trigger

The workflow is triggered in two ways:

1. Manually via `workflow_dispatch`
2. Automatically every 30 minutes via a cron schedule

### Schedule

```yaml
schedule:
  - cron: '30 * * * *'
```

This cron expression means the job will run at the 30-minute mark of every hour.

### Jobs

The workflow calls a reusable workflow (`cron-jobs-30-mins.yml`) which contains several jobs:

#### 1. check-sandbox-status

This job checks the status of review sandboxes and provisions them if necessary.

Key steps:

* Authenticate with DevHub
* Provision sandboxes to "Available" status
* Update sandbox status in a dashboard repository

#### 2. update-scratchorg-status

This job reports the status of scratch orgs  to dev central repository

#### 3. update-sandbox-frontdoor-url

This job updates the frontdoor URL for sandboxes.

#### 4. update-workitem-status

This job updates the status of work items and environment information.

Key steps:

* Report environment information
* Report work item status (likely related to Pull Requests)

#### 5. execute-jobs

This job executes any pending jobs in the repository.

