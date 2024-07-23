# sfops - Daily Job Executor

This workflow is designed to execute various daily jobs related to Salesforce operations, environment management, and reporting.



```
name: sfops - Daily job executor
on:
  schedule:
    - cron: '0 12 * * *' # At 12 00 UTC every day
  workflow_dispatch:

run-name: 'sfops- Execute Daily Jobs'

jobs:
  exec-daily-reports:
      name: 'Execute Daily Reports'
      uses:   ${{ sfops.repo_owner }}/${{ sfops.action_repository }}/.github/workflows/exec-daily-jobs.yml@main
      with:
         sfopsbot-app-id: ${{ vars.SFOPSBOT_APP_ID }}
         metrics-provider: ${{ vars.SFOPS_METRICS_PROVIDER }}
         dashboard-repo: ${{ vars.SFOPS_DASBHOARD_REPO }}
      secrets:
        DATADOG_API_KEY: ${{ secrets.DATADOG_API_KEY }}
        DATADOG_HOST: ${{ secrets.DATADOG_HOST }}
        SFOPSBOT_APP_PRIVATE_KEY: ${{ secrets.SFOPSBOT_APP_PRIVATE_KEY }}

```

### Trigger

The workflow is triggered in two ways:

1. Automatically at 12:00 UTC every day via a cron schedule
2. Manually via `workflow_dispatch`

```yaml
schedule:
  - cron: '0 12 * * *'
```

### Jobs

The  workflow, named "sfops - Daily job executer", calls a reusable workflow `exec-daily-jobs.yml`. and has the following jobs

#### 1. cleanup-runs

This job cleans up workflow runs that are skipped or cancelled

#### 2. report-gh-metrics

This job reports GitHub metrics. It only runs on scheduled events (once a day).

#### 3. report-package-evolution

This job reports on package evolution. to devcentral

