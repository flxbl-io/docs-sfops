# Review Environments

sfops operate with the concept of review environments.  Every change made to your  salesforce repository  is typically validated in a 'dedicated' review environment.  These review environment is used for the following purposes

* validating whether a change is deployable
* validating whether any associated/impacted apex test classes are  working as expected
* validating any end to end testing scenarios
* gather feedback from stakeholders for a change before the change is applied back to the trunk for releasing to production

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption><p>Work items  with status of its checks and associated review orgs</p></figcaption></figure>

As you could see from the above image, each work item (or change or pull request)  is checked in a review environment.   The associated pull request in Github will be provided with the details of the validation job

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

Developers can quickly login in  to the review org using the front door url available on the sfops dev central dashboard as well as a comment on the pull request





