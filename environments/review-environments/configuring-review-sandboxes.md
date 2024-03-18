# Configuring Review Sandboxes

sfops provide you with a feature to create a pool of sandboxes to use as **"Review Sandboxes".**   These sandboxes to the [review category ](https://docs.dxatscale.io/techniques/environment-management) as mentioned in the flxbl environment management guide.\


In your repository, you need to have a file similar to one below  and the path configured in your project workflows for creating sandboxes (sfops-cron-ci-sandbox-create-v2.yml or your variaton).&#x20;

```json
// A sample review sandbox pool config file
[
  {
    "pool": "SALES",
    "count": 13,
    "branch":"main",
    "apexClassId": "01p8w0000009975BAA",
    "sourceSB": "q2upgshape",
    "usersToBeActivated": "master.ninja@flxbl.io,ninja.warrior@adiza.dev"
  },
  {
    "pool": "HOTFIX",
    "branch":"release",
    "count": 1,
    "apexClassId": "01p8w0000009975AAD",
    "sourceSB": "production",
     usersToBeActivated": "master.ninja@flxbl.io,ninja.warrior@adiza.dev"
  }
]
```



The latest schema for sandbox pool definition can be found [here](https://github.com/flxbl-io/json-schemas/blob/main/sandbox-pool-definition.schema.json)

The  `<your-org>sfops-gh-actions/.github/workflows/sandbox-pool-creator.yml@main` workflow accepts a JSON configuration file that defines settings and attributes of the sandbox  pool. The properties accepted by the configuration file are shown in the table below.\
\


| pool               | strimg | Name of the sandbox pool                                                                                                                                |
| ------------------ | ------ | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| count              | int    | Number of sandbox that should be attempted to allocate during one execution                                                                             |
| branch             | string | The branch which the pool should be assigned to                                                                                                         |
| apexClassId        | string | Id of an existing class in the source sandbox that need to be invoked after activation of the sandbox                                                   |
| sourceSB           | string | Name of the sandbox that this sandbox has to be cloned from.   If the sandbox has to be created from production, mention this attribute as 'production' |
| usersToBeActivated | string | Only activate the mentioned users in this sandbox and deactivate everyone else                                                                          |

You could have one or more of  pools mentioned in a single configuration file and could have multiple configuration files.  When you have more than one configuration files, ensure that you have replicated the equivalent github workflow and provided the right file path to your config file



{% hint style="warning" %}
Review sandboxes are dedicated to a single PR, if you would like to reuse  review sandboxes (not recommended) due to shortage of licenses or due to issues with provisioning sandboxes, please set 'SFOPS\_RETURN\_REVIEW\_BACK\_TO\_POOL' to true in your Actions/Variables
{% endhint %}
