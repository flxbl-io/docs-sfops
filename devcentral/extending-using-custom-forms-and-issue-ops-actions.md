# Extending using  Custom Forms and  Issue Ops Actions

This comprehensive guide will walk you through the process of creating custom forms that render in your Dev Central dashboard and setting up custom actions triggered by those forms.

By following this guide, you can create custom forms that render in your Dev Central dashboard and set up custom actions triggered by those forms. This approach allows you to extend the functionality of your **sfops** workflow to meet your specific project needs while leveraging the power of the sfops issueAnalyzer to process and utilize form data efficiently.

Remember that custom forms and actions should be designed with your team's specific workflows and requirements in mind. Regularly gather feedback from users and iterate on your custom forms and actions to ensure they continue to meet your team's evolving needs.

### Creating a Custom Form

{% hint style="info" %}
For self-managed instances, please create the forms yaml file in dashboard/\_forms in your 'sfops' repository
{% endhint %}

1. Create a new YAML file in the `_forms` directory of your dev central repository. The file name will be used as the form identifier, so choose a descriptive name (e.g., `request-custom-action.yml`).
2.  Define the form structure in the YAML file using the following syntax:

    ```yaml
    name: Request Custom Action
    description: This form allows you to request a custom action.
    title: "Custom Action Request"
    labels: ["custom", "request"]
    body:
      - type: input
        id: name
        attributes:
          label: Name
          description: Enter your name
        validations:
          required: true
      - type: textarea
        id: description
        attributes:
          label: Description
          description: Provide a description of the custom action
        validations:
          required: true
      - type: dropdown
        id: priority
        attributes:
          label: Priority
          description: Select the priority level
          options:
            - High
            - Medium
            - Low
      - type: checkboxes
        id: options
        attributes:
          label: Options
          description: Select the applicable options
          options:
            - label: Option 1
            - label: Option 2
            - label: Option 3
    ```

    Customize the form fields based on your requirements. Here are some commonly used field types and their behaviors:

    * `input`: A single-line text input field. You can specify validations like `required` to make the field mandatory.
    * `textarea`: A multi-line text input field for longer descriptions or comments.
    * `dropdown`: A dropdown menu with predefined options. You can specify the `options` array to define the available choices.
    * `checkboxes`: A set of checkboxes allowing multiple selections. Each checkbox is defined within the `options` array.
    * `select`: A dropdown menu with support for multiple selections. The `options` array defines the available choices, and you can group related options using the `optgroup` element.
    * `hiddenInput`: A hidden input field that is not visible to the user but can store predefined values.

### Special Field IDs and Dynamic Options

In addition to the standard form fields, you can leverage special field IDs to populate form options dynamically based on external data sources. This allows for more flexible and data-driven form configurations. Here are some special field IDs you can use:

1. `tag` field:
   * Use the `tag` field ID to populate options based on available tags from the `scratchOrgStatus.json` file.
   * The field type will be automatically set to `dropdown`.
   *   Example:

       ```yaml
       - type: dropdown
         id: tag
         attributes:
           label: Select a tag
           description: Choose a tag from the available options
       ```
2. `sourceSB` field:
   * Use the `sourceSB` field ID to populate options based on snapshot environments from the `envInfos.json` file.
   * The field type will be automatically set to `dropdown`.
   *   Example:

       ```yaml
       - type: dropdown
         id: sourceSB
         attributes:
           label: Select a source sandbox
           description: Choose a source sandbox from the available options
       ```
3. `sourceBranch` field:
   * Use the `sourceBranch` field ID to populate options based on available branches from the `branches.json` file.
   * The field type will be automatically set to `dropdown`.
   *   Example:

       ```yaml
       - type: dropdown
         id: sourceBranch
         attributes:
           label: Select a source branch
           description: Choose a source branch from the available options
       ```
4. `envs` field:
   * Use the `envs` field ID to populate options based on environments from `envInfos.json`, available developer sandboxes from `developerSandboxes.json`, and available CI sandboxes from `ciSandboxes.json`.
   * The field type will be automatically set to `select` for multi-select functionality.
   * Options will be grouped based on their type (e.g., "Developer", "Review") using the `optgroup` element.
   *   Example:

       ```yaml
       - type: select
         id: envs
         attributes:
           label: Select environments
           description: Choose one or more environments from the available options
           multiple: true
       ```
5. `domain` field:
   * Use the `domain` field ID to populate options based on available domains from the `domains.json` file.
   * The field type will be automatically set to `dropdown`.
   *   Example:

       ```yaml
       - type: dropdown
         id: domain
         attributes:
           label: Select a domain
           description: Choose a domain from the available options
       ```

By utilizing these special field IDs, you can create dynamic form fields that adapt to your project's specific data and configurations.

### Creating a Custom Action

1. Create a new workflow file (e.g., `custom-action-workflow.yml`) in the `.github/workflows` directory of your project repository.
2.  Use the following template as a starting point for your custom action workflow:

    ```yaml
    name: Custom Action Workflow

    on:
      issues:
        types: [opened, reopened]

    jobs:
      analyze_issue:
        runs-on: ubuntu-latest
        outputs:
          sfops_issue: ${{ steps.sfops_issue_analyzer.outputs.sfops_issue }}
          name: ${{ steps.sfops_issue_analyzer.outputs.sfops_issue_name }}
          description: ${{ steps.sfops_issue_analyzer.outputs.sfops_issue_description }}
          priority: ${{ steps.sfops_issue_analyzer.outputs.sfops_issue_priority }}
          options: ${{ steps.sfops_issue_analyzer.outputs.sfops_issue_options }}
          issue_number: ${{ steps.sfops_issue_analyzer.outputs.sfops_issue_issueNumber }}
          repo_owner: ${{ steps.sfops_issue_analyzer.outputs.sfops_issue_repoOwner }}
          repo_name: ${{ steps.sfops_issue_analyzer.outputs.sfops_issue_repoName }}
          issue_creator: ${{ steps.sfops_issue_analyzer.outputs.sfops_issue_issueCreator }}
        steps:
          - uses: actions/create-github-app-token@v1
            id: app-token
            with:
              app-id: ${{ inputs.sfopsbot-app-id }}
              private-key: ${{ secrets.SFOPSBOT_APP_PRIVATE_KEY }}
              owner: ${{ github.repository_owner }}

          - name: Analyze Issue
            uses: ${{ sfops.repo_owner }}/${{ sfops.action_repository }}/issueAnalyzer@main
            id: sfops_issue_analyzer
            with:
              repo_owner: ${{ github.repository_owner }}
              repo_name: ${{ github.event.repository.name }}
              issue: ${{ github.event.issue.number }}
              token: ${{ steps.app-token.outputs.token }}
              include_issues: 'your-custom-form-id'

      execute_custom_action:
        needs: analyze_issue
        if: needs.analyze_issue.outputs.sfops_issue == 'true'
        runs-on: ubuntu-latest
        steps:
          - name: Checkout Repository
            uses: actions/checkout@v4

          - name: Execute Custom Action
            run: |
              echo "Executing custom action..."
              echo "Name: ${{ needs.analyze_issue.outputs.name }}"
              echo "Description: ${{ needs.analyze_issue.outputs.description }}"
              echo "Priority: ${{ needs.analyze_issue.outputs.priority }}"
              echo "Options: ${{ needs.analyze_issue.outputs.options }}"
              echo "Issue Number: ${{ needs.analyze_issue.outputs.issue_number }}"
              echo "Repository: ${{ needs.analyze_issue.outputs.repo_owner }}/${{ needs.analyze_issue.outputs.repo_name }}"
              echo "Issue Creator: ${{ needs.analyze_issue.outputs.issue_creator }}"

          - name: Add Comment to Issue
            uses: ${{ sfops.repo_owner }}/${{ sfops.action_repository }}/add-pr-comment@main
            with:
              issue: ${{ needs.analyze_issue.outputs.issue_number }}
              repo-token: ${{ steps.app-token.outputs.token }}
              message: |
                Custom action executed successfully!
    ```
3.  Customize the workflow:

    a. In the `on` section, the workflow is triggered when issues are opened or reopened. You can adjust this as needed.

    b. In the `analyze_issue` job:

    * Replace `your-custom-form-id` in the `include_issues` parameter with the ID of your custom form (the filename without the .yml extension).
    * This job uses the `issueAnalyzer` action to determine if the issue is related to your custom form and extracts the form data.
    * In the `outputs` section, define all the form fields and metadata you want to make available to subsequent jobs. Each output should correspond to a field in your form or metadata provided by the issueAnalyzer.

    c. In the `execute_custom_action` job:

    * This job only runs if the issue is identified as a custom form submission (`sfops_issue == 'true'`).
    * You can directly access all form fields and metadata defined in the `outputs` of the `analyze_issue` job using `needs.analyze_issue.outputs.<field_name>`.

    d. Understanding the issueAnalyzer output: The issueAnalyzer processes the form data and makes it available as outputs. Here's how the data is structured:

    * `sfops_issue`: A boolean value indicating whether the issue is a valid SFOps issue (based on include/exclude rules).
    * Individual form fields: Each form field is available as a separate output with the prefix `sfops_issue_`.
    * Metadata: Additional information like `issueNumber`, `repoOwner`, `repoName`, and `issueCreator` are also available as outputs.

    e. Accessing and using form data in your custom action:

    You can access the form data in your custom action steps using the `needs` context. Here's an example of how to use the parsed data:

    ```yaml
    - name: Execute Custom Action
      run: |
        echo "Executing custom action..."
        echo "Name: ${{ needs.analyze_issue.outputs.name }}"
        echo "Description: ${{ needs.analyze_issue.outputs.description }}"
        echo "Priority: ${{ needs.analyze_issue.outputs.priority }}"
        echo "Options: ${{ needs.analyze_issue.outputs.options }}"
        echo "Issue Number: ${{ needs.analyze_issue.outputs.issue_number }}"
        echo "Repository: ${{ needs.analyze_issue.outputs.repo_owner }}/${{ needs.analyze_issue.outputs.repo_name }}"
        echo "Issue Creator: ${{ needs.analyze_issue.outputs.issue_creator }}"

        # Use the data in your custom logic
        if [ "${{ needs.analyze_issue.outputs.priority }}" = "High" ]; then
          echo "High priority action required!"
          # Add your high-priority action logic here
        fi
    ```

    This example demonstrates how to access various form fields and metadata directly from the job outputs.
4. Adjust the permissions and authentication as needed for your specific use case.
5. Commit and push the changes to your project repository.

### How the issueAnalyzer Works

The issueAnalyzer is a crucial component in processing custom forms. Here's an overview of its functionality:

1. It fetches the issue details using the provided issue number, repository owner, and repository name.
2. It looks for a JSON payload in the issue body, which is typically enclosed in HTML comments (`<!-- -->`).
3. If a valid JSON payload is found, it processes the data and sets various outputs:
   * `sfops_issue`: Set to "true" if the issue is a valid SFOps issue, "false" otherwise.
   * Individual form fields: Each field from the form is set as a separate output with the prefix `sfops_issue_`.
4. It adds some additional metadata as outputs:
   * `sfops_issue_issueNumber`: The number of the current issue.
   * `sfops_issue_repoOwner`: The owner of the repository.
   * `sfops_issue_repoName`: The name of the repository.
   * `sfops_issue_issueCreator`: The GitHub username of the person who created the issue.
5. If the `envs` field is not present in the form data, it defaults to `["devhub"]`.
6. The analyzer respects the `include_issues` and `exclude_issues` inputs to determine whether an issue should be processed or not.

By leveraging the issueAnalyzer, your custom action can easily access and use the form data submitted by users, allowing you to create powerful and flexible workflows tailored to your specific needs.

### Best Practices

1. Keep your custom forms focused and concise, collecting only the necessary information.
2. Use meaningful labels and descriptions for form fields to guide users.
3. Leverage dynamic options when possible to reduce manual maintenance of form options.
4. In your custom action workflow, add appropriate error handling and logging.
5. Consider adding status checks or notifications to keep users informed about the progress of their request.
6. Regularly review and update your custom forms and actions to ensure they remain relevant and efficient.
7. Use the `include_issues` parameter in the issueAnalyzer step to ensure only relevant issues trigger your custom action.
8. When processing form data, always validate inputs to ensure data integrity and security.
9. Utilize the metadata provided by the issueAnalyzer (such as `issue_creator` and `repo_name`) to add context to your custom actions.
10. If your custom action involves sensitive operations, consider adding approval steps or additional security checks.
11. Define all necessary form fields and metadata as outputs in the `analyze_issue` job to make them easily accessible in subsequent jobs.
12. Use meaningful names for your outputs to make the workflow more readable and maintainable.

