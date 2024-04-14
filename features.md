---
description: >-
  sfops features a comprehensive list of features that makes ALM-related process
  a breeze in your Salesforce project.
---

# Features

sfops is built on top of [sfp](https://app.gitbook.com/o/fKx1Ub4x8BCvXSpvO1Hz/s/YLI5Ts7pWhWQV9UaBn3H/), which means it has all the great features that you already love, when it comes to orchestrating packages.  By combining the extensibility of GitHub actions, and the simplicity of GitHub pages, get ready for an amazing set of features.

## Environment Management

sfops features comprehensive scratch org pools, sandbox pools that allows to manage a pool of orgs ready for use as developer/review environments saving considerable amount of time and unlocking never seen before workflows for your salesforce projects

## Dev Central

sfops features a static web app deployed to GitHub pages that enables team members with visibility into your ALM process.

### **Dashboards**

* **Package Evolution:** Track the progression and updates of your Salesforce packages.
* **CI/CD Performance:** Monitor the efficiency and success rates of your continuous integration and deployment processes.
* **Package Metrics:** Analyze detailed metrics related to package usage and performance.
* **Platform Overview:** Get a holistic view of your Salesforce environment and activities.
* _Extensible to Custom Dashboards:_ Tailor additional dashboards specific to your organization's needs.

### **Release Management**

* **Release Candidates:** View all release definitions and associated work items. Initiate multiple release candidates directly from the dashboard.
* **Releases:**  View all past releases and associated work items linked to your issue tracker of choice

### &#x20;**Development Insights**

* **Eyeview:** Lists all active and recently closed pull requests (last 48 hours), including detailed metadata impacts per PR to help developers resolve conflicts efficiently.
* **WorkItem View:** List all the status of open PRs and the status of build jobs associated with any merged PRs.
* **sfdx-project.json Visualization:** Displays your project configuration in a user-friendly tabular format, showing package domains and types.

### **Reports**

* **Apex Test Reports:** Provides daily summaries of Apex test executions in test-designated environments.
* **PMD Reports:** Delivers static analysis reports from PMD to help maintain high code quality across the codebase.

## IssueOps

The `sfops` IssueOps deployed in your GitHub repository as issue templates and associated actions streamlines and automates key Salesforce operations. This  allows team members to efficiently manage package installations, environment configurations, release processes, and user permissions by simply initiating requests via pre-defined issue templates.

### Package/Artifact Installation

* **Initiate `sfp artifact` Installation**: Request the installation of  `sfp` artifacts into any environment
* **Install a `2GP` Package**: Request the installation of 2nd Generation Package (2GP) solutions.

### Environment Management

* **Create New Environments**: Request the creation of new environments in GitHub and associated environments into dev/test/release categories
* **Request a New Developer Sandbox**: Request the creation of new developer sandboxes for testing and development purposes.
* **Request a Scratch Org  from pool**:  Easily fetch scratch from a defined pool

### Release Management

* **Release Branch Management**:
  * **Request a Release Branch for Hotfixes**: Initiate the creation of a branch specifically for applying hotfixes.
* **Deployment Requests**:
  * **Deploy into Release Environments**: Request deployment into environments designated for final release testing.
  * **Deploy into Non-Pipeline Linked Org**: Request deployment into organizations that are not linked to the main pipeline.
  * **Update Release Names**: Request updates to the names of existing releases for better clarity or tracking.

### User and Developer Permissions

* **Permissions Adjustment**:
  * **Elevate User Privileges in Production**: Request an increase in user privileges within production environments.
  * **Freeze/Unfreeze Users in Environments**: Manage user access by freezing or unfreezing user accounts in specific environments.

\
