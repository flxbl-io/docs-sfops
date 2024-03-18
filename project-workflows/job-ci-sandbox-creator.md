# sfops -  Review Sandbox - Creator

{% hint style="info" %}
* Read more about Review Sandboxes [here](../environments/review-environments/)
* This job in some projects may be called  sfops - CI Sandbox - creator
{% endhint %}

The sfops - CI Sandbox - creator is a  GitHub Actions workflow in your project repository that triggers the creation of review sandboxes&#x20;

<pre><code>//Contents of sfops - Review Sandbox - Creator
name: 'sfops - Review Sandbox - Creator'

# Read name of sandbox
on:
  workflow_dispatch:
  schedule:
      - cron: '0 6 * * *'   

#Set the environment variables for tracking metrics
env:
  SFPOWERSCRIPTS_DATADOG: 'true'
  SFPOWERSCRIPTS_DATADOG_HOST: '${{ secrets.DATADOG_HOST }}'
  SFPOWERSCRIPTS_DATADOG_API_KEY: '${{ secrets.DATADOG_API_KEY }}'

jobs:

  create-review-sandbox:
    name: 'Create Review Sandboxes'
    uses:   flxbl-io/sfops-gh-actions/.github/workflows/sandbox-pool-creator.yml@main
    with:
      sfopsbot-app-id: ${{ vars.SFOPSBOT_APP_ID }}
     <a data-footnote-ref href="#user-content-fn-1"> path-to-config : "config/sandbox-pool-config.json"</a>
      dashboard-repo:  ${{ vars.SFOPS_DASHBOARD_REPO }}
      metrics-provider: ${{ vars.SFOPS_METRICS_PROVIDER }}
    secrets:
      DEVHUB_SFDX_AUTH_URL: ${{ secrets.DEVHUB_SFDX_AUTH_URL }}
      DATADOG_API_KEY: ${{ secrets.DATADOG_API_KEY }}
      DATADOG_HOST: ${{ secrets.DATADOG_HOST }}
      SFOPSBOT_APP_PRIVATE_KEY: ${{ secrets.SFOPSBOT_APP_PRIVATE_KEY }}
      NPM_TOKEN: ${{ secrets.GITHUB_TOKEN }}    

</code></pre>

\
The above code shows the basic configuration of this workflow. It utilizes sandbox-pool-creator.yml from the sfops-gh-action repository.

The worklfow is typically configured to operate  at scheduled intervals or when invoked manually. The underyling workflow will attempt to create the sandbox requested as per the provided pool config





[^1]: Path to pool configuration

