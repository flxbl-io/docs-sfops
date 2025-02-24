# January25



{% hint style="info" %}
sfops January 25 have now been patched to v31.0.23with sfp v47.3.5 with fixes to

* Unable to set Field History Tracking for Unlocked Packages ([https://source.flxbl.io/flxbl/sfp-pro/issues/15#issuecomment-336](https://source.flxbl.io/flxbl/sfp-pro/issues/15#issuecomment-336))
* Unable to retrieve profiles in certain orgs&#x20;
* Hotfix release candidates are not visible in DevCentral&#x20;
* Patches to Lock/Deployment Issues in v47.3.2 of sfp
{% endhint %}

\
\
sfops January 25 (v31.0.0) bring the following features/fixes

### sfops

:warning: **Breaking Changes in sfp** \
\
sfp now requires sandbox and branch names to be encoded, so that sandboxes could be identified properly. This requires a migration script to be executed on all project repo's where sfp is  installed

[https://source.flxbl.io/flxbl/sfops/issues/42](https://source.flxbl.io/flxbl/sfops/issues/42)

Please download the migration script provided along with sfp repo

{% embed url="https://source.flxbl.io/flxbl/sfp-pro/releases/download/v47.2.5/flxbl-io-migrate-sandbox-pool-vars-1.0.0.tgz" %}

Instructions on using the script are available at&#x20;

{% embed url="https://source.flxbl.io/flxbl/sfp-pro/src/commit/4ce681a1c4ad02388cca81a8ab75dcadea736698/support-packages/migrate-sandbox-pool-vars/README.md" %}



**Duplicate Checks powered by sfp**

<figure><img src="../.gitbook/assets/CleanShot 2025-02-05 at 23.19.42@2x.png" alt=""><figcaption><p>Check integration with GitHub</p></figcaption></figure>

sfops duplicate check is now enhanced as a GitHub Check, providing better visibility and accomodate aliasified/unclaimed packages.  As the command is utilizing sfp, you could read more about it here\
\
[https://docs.flxbl.io/sfp/analysing-a-project/duplicate-check](https://docs.flxbl.io/sfp/analysing-a-project/duplicate-check)

**sfp upgraded to v47.2.0**

sfops is now powered by sfp v47.2.0 which now supports updated Salesforce libs, in turn allowing you to experiment with Decomposed Metadata types ([https://developer.salesforce.com/docs/atlas.en-us.sfdx\_dev.meta/sfdx\_dev/sfdx\_dev\_ws\_decomposed\_md\_types.htm](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_ws_decomposed_md_types.htm))

#### Bug Fixes

* **actions:** ensure comments more than 64k chars is truncated ([https://source.flxbl.io/flxbl/sfops/issues/43](https://source.flxbl.io/flxbl/sfops/issues/43))
* **review:** add a display of branch in sandbox pool  page&#x20;
* workflow: fix issue with release on any en requiring additional plugins [https://source.flxbl.io/flxbl/sfops/issues/44](https://source.flxbl.io/flxbl/sfops/issues/44)





