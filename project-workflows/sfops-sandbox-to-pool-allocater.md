# sfops - Sandbox To Pool - Allocater

This workflow is  a scheduled job that is set to run every 30 mins to check the status of  sandboxes in DevHub and update the status of sandboxes in Github

```
name: 'sfops - CI Sandbox - Allocate to Pool'

# Read name of sandbox
on:
  workflow_dispatch:
  schedule:
      - cron: '*/30 * * * *'   


jobs:
  allocate-ci-sandbox-to-pool:
    name: 'Allocate  any created CI Sandboxe to pool'
    uses:   flxbl-io/sfops-gh-actions/.github/workflows/sandbox-pool-allocater.yml@main
    with:
      sfopsbot-app-id: ${{ vars.SFOPSBOT_APP_ID }}
      path-to-config : "config/sandbox-pool-config.json"
      metrics-provider: ${{ vars.SFOPS_METRICS_PROVIDER }}
      dashboard-repo: ${{ vars.SFOPS_DASHBOARD_REPO }}
    secrets:
      DEVHUB_SFDX_AUTH_URL: ${{ secrets.DEVHUB_SFDX_AUTH_URL }}
      DATADOG_API_KEY: ${{ secrets.DATADOG_API_KEY }}
      DATADOG_HOST: ${{ secrets.DATADOG_HOST }}
      SFOPSBOT_APP_PRIVATE_KEY: ${{ secrets.SFOPSBOT_APP_PRIVATE_KEY }}
      NPM_TOKEN: ${{ secrets.GITHUB_TOKEN }}    

```

\
The job is configured to run every 30 minutes by default. While it's possible to adjust this interval, we recommend against extending it further.\
\
The associated workflow will check the status of sandboxes in production,  and update the status of the action variables, updates the sfops dashbard
