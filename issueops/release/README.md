# Release IssueOps

Release IssueOps provide self-service release management and deployment operations.

![Release IssueOps Catalog](../../.gitbook/assets/issueops-release-catalog.png)

## Available Operations

| Operation | Issue ID | Description |
|-----------|----------|-------------|
| Request Release Branch | `request-a-release-branch` | Create patch release branch for hotfixes |
| Release to Environments | `request-a-release` | Deploy release to environments in release category |
| Update Release Name | `update-release-name` | Update release definition prefix after sprint |

## Required Configuration

### Secrets
- `DEVHUB_SFDX_AUTH_URL` - For release operations
- Target environment authentication URLs

### Variables
- `SFOPSBOT_APP_ID` - GitHub App ID
- `RELEASENAME` - Current release name variable

## Usage

These operations are enabled by default in sfops. Users can submit requests through:
- **DevCentral**: Navigate to Service Catalogue → Release
- **Direct Issue**: Create an issue with the appropriate JSON payload

## Related Documentation

- [Available IssueOps](../available-issueops.md)
- [Building Custom IssueOps](../building-custom-issueops.md)
- [Release a Domain](release-a-domain.md)
- [Hotfix Workflow](hotfix-workflow.md)