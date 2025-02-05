# January25

sfops January 25 (v31.0.0) bring the following features/fixes

### sfops

:warning: **Breaking Changes in sfp** \
\
sfp now requires sandbox and branch names to be encoded, so that sandboxes could be identified properly. This requires a migration script to be executed on all project repo's where sfp is  installed

[https://source.flxbl.io/flxbl/sfops/issues/42](https://source.flxbl.io/flxbl/sfops/issues/42)

Please follow the script here

[https://gist.github.com/azlam-abdulsalam/1b8b83c8048abd16235b8b2153afe9c3](https://gist.github.com/azlam-abdulsalam/1b8b83c8048abd16235b8b2153afe9c3)



**Duplicate Checks powered by sfp**

<figure><img src="../.gitbook/assets/CleanShot 2025-02-05 at 23.19.42@2x.png" alt=""><figcaption><p>Check integration with GitHub</p></figcaption></figure>

sfops duplicate check is now enhanced as a GitHub Check, providing better visibility and accomodate aliasified/unclaimed packages.  As the command is utilizing sfp, you could [https://docs.flxbl.io/sfp/analysing-a-project/duplicate-check](https://docs.flxbl.io/sfp/analysing-a-project/duplicate-check)

**sfp upgraded to v47.2.0**

sfops is now powered by sfp v47.2.0 which now supports updated Salesforce libs, in turn allowing you to experiment with Decomposed Metadata types ([https://developer.salesforce.com/docs/atlas.en-us.sfdx\_dev.meta/sfdx\_dev/sfdx\_dev\_ws\_decomposed\_md\_types.htm](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_ws_decomposed_md_types.htm))

#### Bug Fixes

* **actions:** ensure comments more than 64k chars is truncated ([https://source.flxbl.io/flxbl/sfops/issues/43](https://source.flxbl.io/flxbl/sfops/issues/43))
* **review:** add a display of branch in sandbox pool  page&#x20;
* workflow: fix issue with release on any en requiring additional plugins [https://source.flxbl.io/flxbl/sfops/issues/44](https://source.flxbl.io/flxbl/sfops/issues/44)





