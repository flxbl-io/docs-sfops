# Building Custom IssueOps

Create custom IssueOps by building GitHub Actions workflows that respond to issues with structured data. No modifications to sfp required.

## How It Works

1. User creates an issue with JSON payload (via DevCentral form or directly)
2. GitHub Actions workflow triggers on issue events
3. Workflow parses the JSON and executes custom logic
4. Workflow posts updates back to the issue

## DevCentral Integration

Custom IssueOps can integrate with DevCentral in two ways:

**For Built-in IssueOps:** DevCentral automatically displays forms for standard operations (scratch orgs, packages, etc.) in the Service Catalogue.

**For Custom IssueOps:** Add form definitions to your DevCentral repository's `_forms` directory. These will appear in the Service Catalogue, allowing users to submit requests through a user-friendly interface instead of writing JSON.

## Complete Example: Custom Deployment Workflow

Here's a working example of a custom IssueOps that deploys to an environment.

> **Note**: This example shows how to create a completely custom IssueOps workflow. For standard operations like scratch org requests or package installations, use the built-in IssueOps provided by sfops.

### 1. Create the Issue Form (Optional)

For user-friendly forms, add this file to your DevCentral repository's `_forms/custom-deploy.yml`:

```yaml
name: Custom Deployment Request
description: Deploy a specific version to an environment
title: "[DEPLOY] "
labels: ["deployment", "custom-ops"]
body:
  - type: markdown
    attributes:
      value: |
        ## Deployment Request
        Use this form to deploy a specific version to an environment.

  - type: input
    id: version
    attributes:
      label: Version
      description: The release version to deploy
      placeholder: "v1.2.3"
    validations:
      required: true

  - type: dropdown
    id: environment
    attributes:
      label: Target Environment
      description: Select the environment to deploy to
      options:
        - dev
        - qa
        - staging
        - prod
    validations:
      required: true

  - type: input
    id: branch
    attributes:
      label: Branch
      description: Source branch (optional, defaults to main)
      placeholder: "main"
    validations:
      required: false

  - type: checkboxes
    id: confirmations
    attributes:
      label: Pre-deployment Checklist
      description: Please confirm
      options:
        - label: I have verified this version has passed all tests
          required: true
        - label: I have approval for production deployments (if applicable)
          required: false
```

### 2. Create the Workflow

Add this file to `.github/workflows/custom-deploy.yml`:

```yaml
name: Custom Deploy IssueOps

on:
  issues:
    types: [opened, reopened]

jobs:
  process_custom_deploy:
    # Only run if issue contains our custom ID
    if: contains(github.event.issue.body, '"id":"custom-deploy"')
    runs-on: ubuntu-latest

    steps:
      - name: Parse Issue Body
        id: parse
        uses: actions/github-script@v7
        with:
          script: |
            const body = context.payload.issue.body;
            const jsonMatch = body.match(/<!--\s*({[\s\S]*?})\s*-->/);

            if (!jsonMatch) {
              core.setFailed('No JSON payload found in issue');
              return;
            }

            const payload = JSON.parse(jsonMatch[1]);

            // Validate required fields
            if (payload.id !== 'custom-deploy') {
              core.setFailed('Not a custom-deploy request');
              return;
            }

            if (!payload.version || !payload.environment) {
              core.setFailed('Missing required fields: version and environment');
              return;
            }

            // Set outputs for next steps
            core.setOutput('version', payload.version);
            core.setOutput('environment', payload.environment);
            core.setOutput('branch', payload.branch || 'main');

      - name: Post Processing Comment
        uses: actions/github-script@v7
        with:
          script: |
            await github.rest.issues.createComment({
              owner: context.repo.owner,
              repo: context.repo.repo,
              issue_number: context.issue.number,
              body: `🔄 Processing deployment request:
              - Version: ${{ steps.parse.outputs.version }}
              - Environment: ${{ steps.parse.outputs.environment }}
              - Branch: ${{ steps.parse.outputs.branch }}

              Please wait while I deploy...`
            });

      - name: Checkout Code
        uses: actions/checkout@v4
        with:
          ref: ${{ steps.parse.outputs.branch }}

      - name: Authenticate to Salesforce
        uses: flxbl-io/sfops-gh-actions/auth-hybrid@main
        with:
          environment: ${{ steps.parse.outputs.environment }}

      - name: Deploy Version
        id: deploy
        run: |
          # Your deployment logic here
          echo "Deploying version ${{ steps.parse.outputs.version }}"

          # Example: Deploy using sfp
          sfp release \
            --targetorg ${{ steps.parse.outputs.environment }} \
            --releasedefn ${{ steps.parse.outputs.version }}

      - name: Post Success Comment
        if: success()
        uses: actions/github-script@v7
        with:
          script: |
            await github.rest.issues.createComment({
              owner: context.repo.owner,
              repo: context.repo.repo,
              issue_number: context.issue.number,
              body: `✅ Deployment completed successfully!

              **Deployment Details:**
              - Version: ${{ steps.parse.outputs.version }}
              - Environment: ${{ steps.parse.outputs.environment }}
              - Status: Success
              - Time: ${new Date().toISOString()}

              You can now access the environment and verify the deployment.`
            });

            // Close the issue
            await github.rest.issues.update({
              owner: context.repo.owner,
              repo: context.repo.repo,
              issue_number: context.issue.number,
              state: 'closed'
            });

      - name: Post Error Comment
        if: failure()
        uses: actions/github-script@v7
        with:
          script: |
            await github.rest.issues.createComment({
              owner: context.repo.owner,
              repo: context.repo.repo,
              issue_number: context.issue.number,
              body: `❌ Deployment failed!

              Please check the [workflow logs](${{ github.server_url }}/${{ github.repository }}/actions/runs/${{ github.run_id }}) for details.

              You may need to:
              - Verify the version exists
              - Check environment permissions
              - Review any validation errors`
            });
```

### 3. How Users Submit Requests

Users can submit requests in two ways:

**Option A: Through DevCentral Forms**
Navigate to DevCentral's Service Catalogue and fill out the form.

**Option B: Direct Issue Creation**
Create an issue directly in GitHub with JSON in the body:

```html
Title: [DEPLOY] Production Release v1.2.3

Body:
...form fields displayed...

<!--
{
  "id": "custom-deploy",
  "version": "v1.2.3",
  "environment": "prod",
  "branch": "main"
}
-->
```

The workflow detects this JSON and processes the request.

## Key Concepts

### Parsing JSON from Issues

```javascript
const body = context.payload.issue.body;
const jsonMatch = body.match(/<!--\s*({[\s\S]*?})\s*-->/);
const payload = JSON.parse(jsonMatch[1]);
```

### Validating Requests

Always validate:
- Required fields are present
- Values are in expected format
- User has necessary permissions

### Providing Feedback

Use issue comments to:
- Confirm request received
- Show progress updates
- Report success or failure
- Provide next steps

### Handling Errors

- Post clear error messages
- Include links to logs
- Suggest fixes
- Don't expose sensitive information

## Extending Your Custom IssueOps

### Add Approval Requirements

Check if certain users or teams approved:

```yaml
- name: Check Approval
  uses: actions/github-script@v7
  with:
    script: |
      const comments = await github.rest.issues.listComments({
        owner: context.repo.owner,
        repo: context.repo.repo,
        issue_number: context.issue.number
      });

      const approved = comments.data.some(comment =>
        comment.body.includes('/approve') &&
        ['approver1', 'approver2'].includes(comment.user.login)
      );

      if (!approved) {
        core.setFailed('Deployment requires approval');
      }
```

### Integrate with External Systems

Call webhooks or APIs:

```yaml
- name: Notify External System
  run: |
    curl -X POST https://api.example.com/deployments \
      -H "Authorization: Bearer ${{ secrets.API_TOKEN }}" \
      -H "Content-Type: application/json" \
      -d '{
        "environment": "${{ steps.parse.outputs.environment }}",
        "version": "${{ steps.parse.outputs.version }}",
        "status": "completed"
      }'
```

### Chain with Built-in IssueOps

React to completed built-in IssueOps:

```yaml
on:
  issues:
    types: [closed]

jobs:
  post_processing:
    if: contains(github.event.issue.labels.*.name, 'scratch-org')
    runs-on: ubuntu-latest
    steps:
      # Extract org details from issue comments
      # Run additional setup
```

## Best Practices

1. **Keep it Simple**: Start with basic functionality, add complexity as needed
2. **Validate Everything**: Check inputs before processing
3. **Provide Clear Feedback**: Users should know what's happening
4. **Handle Errors Gracefully**: Anticipate failures and provide helpful messages
5. **Use Secrets**: Never hardcode sensitive data
6. **Test Thoroughly**: Create test issues to verify workflow behavior

## Testing Your Custom IssueOps

1. Create a test issue with your JSON payload
2. Watch the Actions tab for workflow execution
3. Verify comments are posted correctly
4. Confirm the operation completed as expected

## Troubleshooting

**Workflow not triggering:**
- Check the `if` condition matches your JSON
- Verify workflow file is in correct location
- Ensure workflow has necessary permissions

**JSON parsing errors:**
- Validate JSON syntax
- Check for special characters that need escaping
- Ensure JSON is within HTML comment tags

**Operations failing:**
- Review workflow logs in Actions tab
- Check required secrets are configured
- Verify permissions for resources being accessed

## Next Steps

- Customize the example for your needs
- Add additional validation logic
- Integrate with your existing CI/CD pipeline
- Share your custom IssueOps with your team