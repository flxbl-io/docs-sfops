# November 25

## 🆕 Features

### Project Workflows Restructuring

_Available from: v31.11.0_

Project workflows are now organized into versioned directories for clearer separation:

- **`project-workflows/v1/`** - Traditional workflows using static SFDX auth URL secrets
- **`project-workflows/v2/`** - Workflows using sfp-server for centralized authentication

This restructuring makes it easier to maintain and update workflows independently. See the [migration guide](../self-managed-instances/update-instructions/updating-to-v31.11.0.md) for update instructions.

---

### GitHub Copilot Support for AI Analysis

_Available from: v31.11.0_

sfops now supports GitHub Copilot as an AI provider for PR analysis alongside Anthropic Claude. Add the `COPILOT_TOKEN` secret to enable AI-powered code review and error analysis.

For configuration instructions, see: [Configuring LLM Providers - GitHub Copilot](https://docs.flxbl.io/flxbl/sfp/getting-started/configuring-llm-providers#github-copilot)

---


### DevCentral UI Revamp

_Available from: v31.11.0_

DevCentral has been redesigned with:

- **New themes** - Improved visual design and styling
- **No iframe approach** - Direct page rendering for better performance
- **Test results summary page** - New dashboard showing Apex test results across environments
- **Improved package version reports** - Better handling and styling of package information

---

### Enhanced Issue Closing Workflow

_Available from: v31.11.0_

The close issue workflow now supports cleanup across multiple environments when issues are closed. Configure environment-specific auth URL secrets to enable automatic environment cleanup:

- `STAGING_SFDX_AUTH_URL`
- `PREPROD_SFDX_AUTH_URL`
- `UAT_SFDX_AUTH_URL`
- `QA_SFDX_AUTH_URL`
- `IQA_SFDX_AUTH_URL`
- `SIT_SFDX_AUTH_URL`

---

### Post-PR Instructions Action

_Available from: v31.11.0_

New action that posts user-aware welcome messages and instructions on pull requests. Helps onboard contributors with relevant commands and tips based on the PR context.

---

### Authentication Improvements

_Available from: v31.11.0_

- **Hybrid DevHub Authentication** - Workflows now use the `authToDevHubHybrid` action for more flexible authentication
- **Access Token Support** - Updated authentication methods to use access tokens where appropriate
- **Simplified Secrets** - Reduced dependency on static auth URL secrets in v2 workflows

---

## 🔧 Improvements

### Workflow Reliability

- **Container specifications** - Added container specs to issue analyzer and instructions jobs
- **Repository consistency** - Fixed `gh run cancel` commands to specify repository
- **Datadog integration** - Added Datadog environment variables to multiple workflows

---

## 📋 Prerequisites

This release requires **sfp-pro 50.1.0** or later:

- [sfp-pro release-20251218](https://source.flxbl.io/flxbl/sfp-pro/releases/tag/release-20251218)

---

## 📋 Migration Guide

See [Updating to v31.11.0](../self-managed-instances/update-instructions/updating-to-v31.11.0.md) for detailed migration instructions.
