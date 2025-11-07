# Access IssueOps

Access IssueOps provide self-service management of user permissions and access controls across Salesforce environments.

![Access IssueOps Catalog](../../.gitbook/assets/issueops-access-catalog.png)

## Available Operations

| Operation | Issue ID | Description |
|-----------|----------|-------------|
| Request Elevated Privileges | `request-elevated-previleges` | Grant temporary elevated access to production with auto de-escalation |
| Freeze Users | `request-freeze-users` | Freeze users by profile in specified environments |
| Unfreeze Users | `request-unfreeze-users` | Unfreeze previously frozen users based on profiles |

## Required Configuration

### Secrets
- `PROD_SFDX_AUTH_URL` - For production access operations

### Variables
- `SFOPSBOT_APP_ID` - GitHub App ID

## Usage

These operations are enabled by default in sfops. Users can submit requests through:
- **DevCentral**: Navigate to Service Catalogue → Access
- **Direct Issue**: Create an issue with the appropriate JSON payload

## Related Documentation

- [Available IssueOps](../available-issueops.md)
- [Building Custom IssueOps](../building-custom-issueops.md)
- [Request Elevated Privileges](request-elevated-previlege-in-production.md)