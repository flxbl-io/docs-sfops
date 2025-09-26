# Authenticating to Environments

Environments in sfops are authenticated using the credentials stored in Github. This section describes the various mechanisms used for authenticating environments

### Authenticating to Production Environment

If your dev hub and production is on the same org, you do not have to create any additional secrets in the Github Environment 'PROD'. If its different, you need to create a new environment secret titled 'SFDX\_AUTH\_URL' and add the equivalent value.

### Authenticating to Other Environments

Authenticating to any other environments backed by a sandbox, depends on how the sandbox is being created\
\
1\. **Sandboxes created by same production user configured in GitHub**

Sandboxes created by the same production user can be authenticated by using the sandbox name, all that is required is the use of setting up the SBX\_NAME variable in environment as shown below\\

<figure><img src="../.gitbook/assets/EnvVarsSandbox.png" alt=""><figcaption><p>Use of SBX_NAME in Authentication</p></figcaption></figure>

{% hint style="danger" %}
Please note, when a sandbox is refreshed from the UI, irrespective of whether the action was done by the same user configured in Github, sfops will not be able to authenticate to this environment, and you will need to provide SB\_SFDX\_AUTH\_URL as mentioned in #2.
{% endhint %}

2. **Sandboxes created by any other users in production**\
   \
   To authenticate a sandbox that was not created by the configured user in Github, one needs to provide a environment secret **SB\_SFDX\_AUTH\_URL** which can be obtained by using the instructions[ here.](https://docs.flxbl.io/sfp/pools/setting-up-your-salesforce-org-for-scratch-org-pools#generate-sfdx-auth-url-for-pipeline-authentication)



<figure><img src="../.gitbook/assets/EnvSecretsSandbox.png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
**Authentication Methods for Daily Test Runs**

sfops supports two authentication methods for daily test runs:
- **GitHub Secrets Based**: For organizations using GitHub environments
- **SFP Server Based**: For organizations with SFP Server integration
{% endhint %}

#### GitHub Secrets Based Authentication

For GitHub secrets based daily test runs, you need to:

1. **Add repository secrets** for each environment using the exact format: `<ENVIRONMENT_NAME>_SFDX_AUTH_URL`
2. **Pass these secrets** in your daily test workflow

**Important Limitation**: Due to how GitHub Actions handles secrets in reusable workflows, **only these specific environment names and their corresponding secrets are supported**:

| GitHub Environment Name | Required Repository Secret |
|-------------------------|----------------------------|
| **STAGING**             | `STAGING_SFDX_AUTH_URL`    |
| **SIT**                 | `SIT_SFDX_AUTH_URL`        |
| **PREPROD**             | `PREPROD_SFDX_AUTH_URL`    |
| **UAT**                 | `UAT_SFDX_AUTH_URL`        |
| **QA**                  | `QA_SFDX_AUTH_URL`         |
| **IQA**                 | `IQA_SFDX_AUTH_URL`        |

{% hint style="warning" %}
**Custom environment names are not supported in automated workflows**

If you have a sandbox with a custom name (e.g., "myawesomestagingorg"), you must rename it to one of the supported names above (e.g., "STAGING") for daily test runs to work.

This limitation exists because the workflow cannot dynamically pass arbitrary secrets, and only the predefined environment names are configured.
{% endhint %}

Example daily test workflow configuration:
```yaml
apex-test-runs:
  uses: ./.github/workflows/apex-test-on-test-envs.yml@main
  secrets:
    DEVHUB_SFDX_AUTH_URL: ${{ secrets.DEVHUB_SFDX_AUTH_URL }}
    STAGING_SFDX_AUTH_URL: ${{ secrets.STAGING_SFDX_AUTH_URL }}
    UAT_SFDX_AUTH_URL: ${{ secrets.UAT_SFDX_AUTH_URL }}
    # Only the predefined environment names work here
```

#### SFP Server Based Authentication

For SFP Server based daily test runs:
- Authentication is managed centrally through SFP Server
- No need for individual environment secrets in GitHub
- Supports any environment name without restrictions
- Auth URLs are stored and retrieved dynamically from SFP Server

{% hint style="success" %}
**No naming restrictions with SFP Server**

SFP Server removes the environment naming limitations. You can use any sandbox name and the authentication will work seamlessly for daily test runs.
{% endhint %}

Example daily test workflow configuration:
```yaml
apex-test-runs:
  uses: ./.github/workflows/apex-test-on-test-envs-v2.yml@main
  with:
    sfp-server-url: ${{ vars.SFP_SERVER_URL }}
  secrets:
    DEVHUB_SFDX_AUTH_URL: ${{ secrets.DEVHUB_SFDX_AUTH_URL }}
    SFP_SERVER_TOKEN: ${{ secrets.SFP_SERVER_TOKEN }}
```

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>
