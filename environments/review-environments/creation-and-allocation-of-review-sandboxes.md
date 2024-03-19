# Creation and Allocation of Review Sandboxes

The following high level view on how the review sandboxes are created and allocated to the sandbox pools

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption><p>Asynchronous creation and allocation</p></figcaption></figure>

This section outlines the processes involved in the creation, allocation, and management of the lifecycle of Salesforce sandboxes, including conditions for expiry and deletion.

### Process Descriptions

### 1. Sandbox Creation (Review Sandbox - Creator)

The creation process can be initiated either by a scheduled cron job or by a manual trigger.

A predefined number of sandboxes are requested to be created, based on the count specified in the configuration settings. Post creation request, GitHub action variables are updated with sandbox details, marking them as "In Progress".

### 2. Sandbox Allocation and Expiry Check (Sandbox To Pool - Allocator)

This step can be started by a cron job or manually.

The system checks the status of sandboxes in DevHub marked "In Progress". If a sandbox is activated and ready, its status is updated to "Available" in GitHub.

**Expiry Eligibility Criteria**

The system evaluates "Available" sandboxes in GitHub to determine their eligibility for expiry based on the following rules:

1. **Unassigned Sandbox**: If a sandbox is not assigned an issue within 24 hours of its creation, it is eligible for expiry.
2. **Assigned Sandbox**:
   * If the sandbox has an issue assigned but is not extended, it becomes eligible for expiry 24 hours after the issue assignment.
   * If the sandbox is extended post-issue assignment, the expiry eligibility extends to 48 hours from the issue assignment time.

**Expiry and Deletion Process**

* Sandboxes meeting the above criteria are marked as "Expired".
* The system attempts to delete all "Expired" sandboxes.
* If deletion fails, the sandbox remains marked as "Expired" and will be re-attempted for deletion in the next run.
* Upon deletion attempt, whether successful or pending, GitHub action variables associated with the expired sandboxes are removed.

###

