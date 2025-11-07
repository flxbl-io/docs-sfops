# Environment IssueOps

Environment IssueOps provide self-service provisioning and management of development environments.

![Environment IssueOps Catalog](../../.gitbook/assets/issueops-environment-catalog.png)

## Available Operations

| Operation | Issue ID | Description |
|-----------|----------|-------------|
| Request Scratch Org | `request-scratchorg` | Get a scratch org from pool for specified duration |
| Request Dev Sandbox | `request-dev-sandbox` | Provision a developer sandbox from pool or create new |
| Request GitHub Environment | `request-an-env` | Create GitHub environment with variables and approvals |
| Delete Environment | `request-delete-env` | Remove GitHub environment |
| Request from SBX Pool | `request-sbx-pool` | Get sandbox from SFP Server pool |
| Unassign from SBX Pool | `unassign-sbx-pool` | Return sandbox to pool |

## Required Configuration

### Secrets
- `DEVHUB_SFDX_AUTH_URL` - For scratch org operations
- `SFP_SERVER_URL` - For sandbox pool operations (optional)
- `SFP_SERVER_TOKEN` - SFP Server API token (optional)

### Variables
- `SFOPSBOT_APP_ID` - GitHub App ID
- `DEFAULT_DEVHUB_USERNAME` - DevHub username

## Usage

These operations are enabled by default in sfops. Users can submit requests through:
- **DevCentral**: Navigate to Service Catalogue → Environment
- **Direct Issue**: Create an issue with the appropriate JSON payload

## Related Documentation

- [Available IssueOps](../available-issueops.md)
- [Building Custom IssueOps](../building-custom-issueops.md)