---
description: >-
  Learn how to authenticate to Salesforce environments through SFP server
  without locking for read-only operations and concurrent access scenarios
---

# Authentication without Lock

The `authToEnvironmentWithoutLock` action provides streamlined authentication to Salesforce environments through SFP server without acquiring an exclusive lock. This enables multiple workflows to access the same environment simultaneously, making it ideal for read-only operations, monitoring, and scenarios where concurrent access is safe.

## Overview

When performing read-only operations or non-conflicting tasks on Salesforce environments, exclusive locking is often unnecessary and can create bottlenecks. The authentication without lock action provides:

* **Concurrent environment access** allowing multiple workflows to run simultaneously
* **Faster authentication** by skipping the lock acquisition process
* **Read-only safety** for operations that don't modify the environment
* **Reduced wait times** in busy CI/CD pipelines
* **Simplified workflow logic** when locks aren't needed

This makes it perfect for:

* Data queries and exports
* Environment health checks
* Monitoring and reporting
* Test execution (read-only)
* Metadata inspection
* Any non-modifying operations

## How It Works

The action follows this streamlined workflow:

```mermaid
flowchart TD
    Start([Start: Need to authenticate<br/>for read-only access]) --> GetEnv[Request environment details<br/>from SFP server]

    GetEnv --> CheckEnv{Environment<br/>exists?}

    CheckEnv -->|Yes| ExtractAuth[Extract authentication<br/>credentials]
    CheckEnv -->|No| Fail[Fail with error]

    ExtractAuth --> Auth[Authenticate to<br/>Salesforce org]

    Auth --> Output[Output credentials<br/>and org details]

    Output --> End([Ready for concurrent<br/>operations])

    Fail --> EndFail([Authentication failed])

    style ExtractAuth fill:#e1f5e1
    style Auth fill:#e1f5e1
    style Fail fill:#ffe1e1
    style Output fill:#e1f0ff
```

## Prerequisites

Before using this action in your custom workflow, ensure:

1.  **Your workflow uses the sfops Docker image** - Required for SFP CLI and dependencies:

    ```yaml
    jobs:
      your-job:
        runs-on: ubuntu-latest
        container: ${{ sfops.sfops_docker_image }}
    ```
2. **SFP server credentials are configured**:
   * `SFP_SERVER_URL` as a variable
   * `SFP_SERVER_TOKEN` as a secret
3. **Environment is registered in SFP server** - This action only works with environments managed by SFP server

## Referencing the Action

The `authToEnvironmentWithoutLock` action is located in your sfops repository. In all examples below, the action is referenced using:

```yaml
uses: ${{ sfops.repo_owner }}/${{ sfops.action_repository }}/authToEnvironmentWithoutLock@main
```

The `${{ sfops.repo_owner }}/${{ sfops.action_repository }}` template variables are automatically replaced with your organization and sfops repository name (e.g., `flxbl-io/sfops-gh-actions`).

## Basic Usage

### Simple Authentication for Read Operations

The minimal configuration for authentication without locking:

```yaml
name: Query Environment Data
on:
  workflow_dispatch:
    inputs:
      environment:
        description: 'Target environment'
        required: true
        type: string

jobs:
  query-data:
    runs-on: ubuntu-latest
    container: ${{ sfops.sfops_docker_image }}
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Authenticate Without Lock
        id: auth
        uses: ${{ sfops.repo_owner }}/${{ sfops.action_repository }}/authToEnvironmentWithoutLock@main
        with:
          environment: ${{ inputs.environment }}
          repository: ${{ github.repository }}
          sfp-server-url: ${{ vars.SFP_SERVER_URL }}
          sfp-server-token: ${{ secrets.SFP_SERVER_TOKEN }}

      - name: Query Data
        run: |
          # Safe to run concurrently - read-only operation
          sfdx force:data:soql:query \
            --targetusername ${{ steps.auth.outputs.alias }} \
            --query "SELECT COUNT() FROM Account" \
            --json
```

### Parallel Environment Checks

Run concurrent checks across multiple environments:

```yaml
name: Multi-Environment Health Check
on:
  schedule:
    - cron: '0 */6 * * *'  # Every 6 hours

jobs:
  check-environment:
    runs-on: ubuntu-latest
    container: ${{ sfops.sfops_docker_image }}
    strategy:
      matrix:
        environment: [dev, staging, uat, production]
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Authenticate Without Lock
        id: auth
        uses: ${{ sfops.repo_owner }}/${{ sfops.action_repository }}/authToEnvironmentWithoutLock@main
        with:
          environment: ${{ matrix.environment }}
          repository: ${{ github.repository }}
          sfp-server-url: ${{ vars.SFP_SERVER_URL }}
          sfp-server-token: ${{ secrets.SFP_SERVER_TOKEN }}

      - name: Check Environment Health
        run: |
          # Multiple jobs can check different environments simultaneously
          sfdx force:limits:api:display \
            --targetusername ${{ steps.auth.outputs.alias }} \
            --json

      - name: Check Apex Test Coverage
        run: |
          sfdx force:apex:test:report \
            --targetusername ${{ steps.auth.outputs.alias }} \
            --codecoverage \
            --json
```



## Input Reference

| Input              | Required | Default | Description                                |
| ------------------ | -------- | ------- | ------------------------------------------ |
| `environment`      | Yes      | -       | Name of the environment to authenticate to |
| `repository`       | Yes      | -       | Repository name in `owner/repo` format     |
| `sfp-server-url`   | Yes      | -       | URL to your SFP server instance            |
| `sfp-server-token` | Yes      | -       | Authentication token for SFP server        |

## Output Reference

| Output         | Description                       | Example Usage                            |
| -------------- | --------------------------------- | ---------------------------------------- |
| `alias`        | Alias of the authenticated org    | `${{ steps.auth.outputs.alias }}`        |
| `is_active`    | Whether the environment is active | `${{ steps.auth.outputs.is_active }}`    |
| `org_id`       | Salesforce Org ID                 | `${{ steps.auth.outputs.org_id }}`       |
| `instance_url` | Instance URL                      | `${{ steps.auth.outputs.instance_url }}` |
| `login_url`    | Login URL                         | `${{ steps.auth.outputs.login_url }}`    |
| `access_token` | Access token for API calls        | `${{ steps.auth.outputs.access_token }}` |
| `username`     | Username of authenticated user    | `${{ steps.auth.outputs.username }}`     |



## When to Use Lock vs Without Lock

| Use With Lock         | Use Without Lock       |
| --------------------- | ---------------------- |
| Deployments           | Data queries           |
| Data imports          | Data exports           |
| Destructive changes   | Metadata retrieval     |
| Schema modifications  | Environment monitoring |
| Configuration changes | Report generation      |
| Any write operations  | Read-only validation   |

