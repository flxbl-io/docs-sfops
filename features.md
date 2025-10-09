---
description: >-
  sfops delivers enterprise-grade Salesforce DevOps through opinionated,
  GitHub-native workflows that embody Flxbl best practices.
---

# Features

As the official GitHub implementation of Flxbl, sfops delivers enterprise-grade Salesforce DevOps through production-ready, opinionated workflows.

Built on [sfp](https://docs.flxbl.io/sfp)—the package orchestration engine powering Flxbl—sfops combines proven ALM patterns with GitHub's native platform capabilities. The result: comprehensive automation that works out of the box, with the extensibility to adapt to your specific needs.

## Environment Management

sfops manages comprehensive scratch org and sandbox pools, maintaining ready-to-use environments for development and review workflows. This eliminates wait times and enables modern development patterns like ephemeral review environments and pooled developer sandboxes—workflows that simply aren't possible without automation.

Enhanced with [sfp server](https://docs.flxbl.io/sfp-server), sfops maintains persistent environment state that survives across workflow runs:
- **Environment state persistence**: Stores environment metadata, assignments, and pool state outside GitHub's ephemeral workflow context
- **Review environment pooling**: Maintains pool state and assignment metadata for intelligent PR-to-environment matching
- **Distributed lock coordination**: Enables safe concurrent access to shared environments across parallel workflows
- **Cross-workflow context**: Shares environment state and metadata across multiple workflows and repositories

## Dev Central

Dev Central is a GitHub Pages application that provides complete visibility into your ALM process. Built as a static web app, it delivers real-time insights without requiring external hosting or infrastructure.

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

## AI-Powered Code Intelligence

sfops brings AI assistance natively into your pull request workflow. During PR validation, the AI-powered linter automatically analyzes your changes and provides intelligent architectural insights:

- **Smart PR analysis**: Examines changed files in real-time during validation
- **Flxbl framework expertise**: Trained on Salesforce and Flxbl patterns to provide contextual recommendations
- **Actionable insights**: Generates severity-based feedback (info, warning, concern) with specific recommendations
- **GitHub-native integration**: Appears as check annotations alongside standard PR checks—never blocks merges
- **Flexible provider support**: Works with Anthropic Claude (recommended), OpenAI, Amazon Bedrock, or GitHub Copilot

This AI integration runs automatically on every PR, providing experienced architectural guidance without requiring manual code reviews or interrupting developer flow.

## IssueOps

sfops IssueOps transforms GitHub issues into operational workflows. Pre-defined issue templates and associated GitHub Actions automate key Salesforce operations, enabling team members to manage package installations, environment configurations, releases, and permissions through simple, trackable issue requests.

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
