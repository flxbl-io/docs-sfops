# 6. Fetching upstream changes

If you are using a self-managed instance option for sfops, please follow the below instructions to update your GitHub org's with  updates from Flxbl's source repository (source.flxbl.io)

The below process outlines the steps that are required to update  your instance across test and production repositories

1. Please review the changes once you receive a notification on #sfops slack  channel or email regarding the latest release notes.  Please feel free to contact your point of contact for more information on the changes in the releases and applying the same in your org
2.  Each version in sfops repository is tagged with a version number and will have accompanying release notes\


    <figure><img src="../../../.gitbook/assets/Screenshot 2024-06-26 at 10.37.10 PM.png" alt=""><figcaption></figcaption></figure>
3.  In your sfops repository, navigate to actions, and trigger the Check Specific Tag and Create PR workflow.  Please provide the tag as mentioned in the release notes as an input to the workflow. This will in turn create a PR with the necessary changes to be applied to your repository.\
    \


    <figure><img src="../../../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>


4. Review the changes, you might need to align the changes (or resolve conflicts) with any local changes you have made to sfops repository.  Merge the changes to the 'main' branch. This will in turn trigger the following workflows



*   If there are any updates required for the **sfp** images used in sfops, this will result in the triggering of the **Build & Publish Docker Image workflow (trigger-docker.yml).** You will need to approve the image to multiple promotion target such as development, alpha and production. Each stage of the image (or labels to be exact) can be utilized by different sfops-gh-actions (test/production) depending on how its configured in the [variables](../3.-setting-up-sfops-repository.md). Often you will need to wait for the image build  jobs to be completed before proceeding to approve the deployment to test or alpha environments\
    \


    <figure><img src="../../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>


* the **Build & Test** workflows (build.yaml) will also be triggered and will be waiting for approvals to deploy to Test & Alpha environments

<div align="center">

<figure><img src="../../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

</div>

* A **release-please** PR gets created with the changelog and auto-incremented version details against the main branch will be created\


<figure><img src="../../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

6. Merge the release-please PR which will in turn trigger the **release-please** (release-please.yml) workflow and will await approvals to deploy to push changes into your prod configurations of sfops-gh-actions. Please ensure you take the necessary action to promote the docker images (if required) into prod in the trigger-docker.yml workflows

<figure><img src="../../../.gitbook/assets/image (5).png" alt=""><figcaption><p>release-please.yml</p></figcaption></figure>
