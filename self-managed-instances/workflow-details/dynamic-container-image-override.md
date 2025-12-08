# Dynamic Container Image Override

This feature allows administrators to dynamically switch the Docker container images used by sfops workflows **without re-syncing** the sfops-gh-actions repository.

## Use Cases

- **Testing new sfp versions**: Test RC or development images before rolling them out
- **Emergency rollback**: Quickly revert to a previous container version if issues arise
- **Hotfix deployment**: Switch to a patched image immediately across all workflows

## How It Works

After syncing, sfops workflows use a pattern that checks for override variables:

```yaml
container: ${{ vars.SFOPS_DOCKER_IMAGE_OVERRIDE || 'ghcr.io/your-org/sfops:latest' }}
```

- **No override variable set**: Uses the default image (hardcoded during sync)
- **Override variable set**: Uses the override image immediately

## Configuration

### Override Variables

Set these variables in your **sfops-gh-actions** repository (not the project repository):

| Variable                          | Purpose                                |
|-----------------------------------|----------------------------------------|
| `SFOPS_DOCKER_IMAGE_OVERRIDE`     | Override for the full sfops image      |
| `SFOPS_LITE_DOCKER_IMAGE_OVERRIDE`| Override for the sfops-lite image      |

### Setting Up an Override

1. Navigate to your **sfops-gh-actions** repository
2. Go to **Settings** → **Secrets and variables** → **Actions** → **Variables** tab
3. Click **New repository variable**
4. Add the variable name and the full image path as the value

**Example:**
```
Name:  SFOPS_DOCKER_IMAGE_OVERRIDE
Value: ghcr.io/your-org/sfops:v2.5.0-rc1
```

### Removing an Override

To revert to the default image, simply **delete** the override variable from the repository settings. All workflows will immediately use the hardcoded default from the last sync.

## Examples

### Testing a Release Candidate

```
1. Build the new image: ghcr.io/your-org/sfops:v2.5.0-rc1
2. Set variable: SFOPS_DOCKER_IMAGE_OVERRIDE = ghcr.io/your-org/sfops:v2.5.0-rc1
3. Run test workflows to validate the new version
4. If successful, either:
   - Re-sync with the new version as the default, then delete the override
   - Keep the override until next scheduled sync
5. If issues found, delete the variable to revert immediately
```

### Emergency Rollback

```
1. Issue detected with current sfops:latest image
2. Set variable: SFOPS_DOCKER_IMAGE_OVERRIDE = ghcr.io/your-org/sfops:v2.4.0
3. All workflows immediately use v2.4.0
4. Investigate and fix the issue at your own pace
5. Once fixed, delete the override or sync with updated default
```

### Testing Both Image Variants

You can override one or both images independently:

```
# Test only the full image
SFOPS_DOCKER_IMAGE_OVERRIDE = ghcr.io/your-org/sfops:testing

# Test only the lite image
SFOPS_LITE_DOCKER_IMAGE_OVERRIDE = ghcr.io/your-org/sfops-lite:testing

# Test both simultaneously
SFOPS_DOCKER_IMAGE_OVERRIDE = ghcr.io/your-org/sfops:testing
SFOPS_LITE_DOCKER_IMAGE_OVERRIDE = ghcr.io/your-org/sfops-lite:testing
```

## Important Notes

{% hint style="warning" %}
**Scope**: Override variables must be set in the **sfops-gh-actions** repository, not in project repositories. This is a GitHub Actions limitation - the `vars` context in reusable workflows reads from the repository where the workflow file exists.
{% endhint %}

{% hint style="info" %}
**Organization-wide Effect**: When you set an override, it affects **all workflows** using that image across all projects that call the reusable workflows. This is by design for administrative control.
{% endhint %}

{% hint style="info" %}
**No Re-sync Required**: Changes to override variables take effect immediately on the next workflow run. You don't need to re-sync or redeploy anything.
{% endhint %}

## Comparison: Override vs Re-sync

| Aspect              | Override Variable        | Re-sync                     |
|---------------------|-------------------------|-----------------------------|
| Speed               | Immediate               | Requires sync workflow run  |
| Scope               | All workflows           | All workflows               |
| Persistence         | Until variable deleted  | Permanent until next sync   |
| Audit trail         | Variable history        | Git commit history          |
| Best for            | Testing, rollback       | Production releases         |

## Troubleshooting

### Override Not Taking Effect

1. Verify the variable is set in **sfops-gh-actions** repository (not project repo)
2. Ensure the variable name is exactly `SFOPS_DOCKER_IMAGE_OVERRIDE` (case-sensitive)
3. Check that the image path is valid and accessible
4. Trigger a new workflow run (existing runs use the image at start time)

### Image Pull Errors

If workflows fail with image pull errors after setting an override:
1. Verify the image exists in your container registry
2. Check that the image tag is correct
3. Ensure GitHub Actions has permission to pull from that registry
