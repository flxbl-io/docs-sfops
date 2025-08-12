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

If the environment is used assigned to '**release**' category or has been assigned 'test' (daily test run), you also need to set up additional variable `<NAME_OF_THE_ENVIRONMENT>_SFDX_AUTH_URL` as repository secret.  (This is due to a limitation where secrets of the environment cant be acessed without approvals and hence can block automations which require to be run without intervention)\
\
The name of the environments that are supported for this mode should be one of the following

* &#x20;STAGING,
* SIT
* &#x20;PREPROD
* UAT
* QA
* IQA&#x20;
* SIT\


<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>
