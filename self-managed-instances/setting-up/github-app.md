---
layout:
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# 2. Create a GitHub App

This section deals with setting up a GitHub App which is required for sfops to function. sfops require additional permissions which allow to write into other repository such as 'sfops-dev-central' and also permission to trigger workflows etc. These permissions are beyond what is being provided by the built in GITHUB\_TOKEN. A Github App is recommended over using a Service Account and its Personal Access Token, as the service account takes an additional license and has limitations on the api requests.

This guide is crafted to facilitate the user to create a `sfops-bot` GitHub App to automate the worflows provided out of the box with sfops. It provides a step-by-step approach for creating the app, elaborating on the necessary permissions, installation, and secure storage of sensitive information.\
\
You can refer to this link to understand how this work behind the scenes

[https://docs.github.com/en/apps/creating-github-apps/authenticating-with-a-github-app/making-authenticated-api-requests-with-a-github-app-in-a-github-actions-workflow#authenticating-with-a-github-app](https://docs.github.com/en/apps/creating-github-apps/authenticating-with-a-github-app/making-authenticated-api-requests-with-a-github-app-in-a-github-actions-workflow#authenticating-with-a-github-app)\\

## Step-by-Step Creation and Configuration

### **Step 1: Registration of sfops-bot GitHub App**

* Navigate to: **GitHub org → Settings → Developer settings → GitHub Apps → New GitHub App**
  * Direct URL: `https://github.com/organizations/YOUR_ORG/settings/apps/new`
* Configure the app:
  - **GitHub App name**: `sfops-bot`
  - **Homepage URL**: `https://github.com/YOUR_ORG/sfops` (or your organization URL)
  - **Webhook**: Uncheck "Active" (webhooks are not needed for sfops-bot)
* Add an icon and background color in the 'Display Information' to make the app identifiable in your workflows

### **Step 2: Permissions Configuration**

Configure the following permissions for the sfops-bot app:

#### Repository Permissions
| Permission         | Access Level | Purpose                                                                                                                                        |
|--------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
| **Actions**        | Read & Write | Crucial for the app to manage GitHub Actions, which are integral to automation workflows                                                       |
| **Administration** | Read & Write | Required to create GitHub environments via API (IssueOps environment requests)                                                                 |
| **Checks**         | Read & Write | Required for reading check runs and status checks on commits, essential for PR status reporting                                                |
| **Contents**       | Read & Write | Manage code, branches, commits, and merges. This access allows the app to automate code integration processes                                  |
| **Deployments**    | Read & Write | Empowers the app to manage deployments, essential for continuous delivery workflows                                                            |
| **Environments**   | Read & Write | Manage environment settings and protection rules after creation                                                                                |
| **Issues**         | Read & Write | Enable the app to automate issue tracking, commenting, and labeling                                                                            |
| **Projects**       | Read & Write | Allow the app to connect issues to projects for better project management                                                                      |
| **Pull Requests**  | Read & Write | Necessary for the app to automate the handling of pull requests, including merging and labeling                                                |
| **Secrets**        | Read         | Lets the app manage secrets without compromising their security, essential for secure workflows                                                |
| **Variables**      | Read & Write | Permit the app to read the variables in the repo. This is vital for dynamic configuration of the environment and branch related configurations |
| **Workflows**      | Read & Write | Permit the app to update workflow files, which is vital for maintaining automated processes                                                    |

#### Organization Permissions
| Permission   | Access Level | Purpose                                                                   |
|--------------|--------------|---------------------------------------------------------------------------|
| **Projects** | Read & Write | Allow the app to connect issues to projects for better project management |

{% hint style="info" %}
The new GitHub projects are created at organization level, and requires organization permissions
{% endhint %}

### **Step 3: Configure Installation Settings**

* In the **Where can this GitHub App be installed?** section:
  - Select **"Only on this account"** to restrict the app to your organization
  - This ensures the app can only be installed within your organization

### **Step 4: Create the GitHub App**

* Click **"Create GitHub App"** at the bottom of the form
* You'll be redirected to your new app's settings page
* **Keep this tab open** - you'll need information from this page in Step 6

### **Step 5: Install the App**

* Navigate to the 'Install App' tab within your app settings
* Click **"Install"** to initiate the installation process
* Select your organization and choose either:
  - **All repositories** (recommended for simplicity), or
  - **Selected repositories**: Include at minimum:
    - `sfops`
    - `sfops-gh-actions` 
    - `sfops-dev-central`
    - Your Salesforce project repositories

{% hint style="warning" %}
**Important**: After installing the app, if you update permissions later (e.g., adding Checks permission), you must accept the new permissions:

1. Go to any repository where the app is installed (e.g., your project repository)
2. Navigate to: **Repository Settings** → **Integrations** → **Installed GitHub Apps**
3. Click on your sfops-bot app - this will redirect you to the organization-level configuration
4. Review and **accept any pending permission requests**
5. The updated permissions will apply to all repositories where the app is installed

Direct URL: `https://github.com/YOUR_ORG/YOUR_REPO/settings/installations`
{% endhint %}

### **Step 6: Collect App Credentials and Store as Secrets**

#### Collect the required credentials (from Step 4's settings page):
1. **App ID**: Note the App ID displayed at the top of your app's settings page
2. **Private Key**: 
   - Scroll down to the **Private keys** section on your app's settings page
   - Click **"Generate a private key"**
   - The `.pem` file will download automatically - save it securely

#### Store as GitHub secrets:
* Navigate to your `sfops` repository → **Settings → Secrets and variables → Actions**
* Create the following:
  - **`SFOPSBOT_APP_ID`**: Paste the App ID number from the settings page (store as Repository variable)
  - **`SFOPSBOT_APP_PRIVATE_KEY`**: Paste the entire contents of the downloaded `.pem` file (store as Repository secret)

####

The workflows provided by sfops utilizes the above variables to authenticated to do repository operations as provided in the below example\\

```
// Sample code demonstrating how the token is used
      - name: Generate a token
        id: generate_token
        uses: actions/create-github-app-token@v1
        with:
          app-id: ${{ var.SFOPSBOT_APP_ID }}
          private-key: ${{ secrets.SFOPSBOT_APP_PRIVATE_KEY }}

      - name: Use the token
        env:
          GITHUB_TOKEN: ${{ steps.generate_token.outputs.token }}
        run: |
          gh workflow <>
```

## Troubleshooting

### Error: Failed to create Environment - Resource not accessible by integration

You see this error in your workflow logs:

```
Error: Failed to create Environment due to Resource not accessible by integration
https://docs.github.com/rest/deployments/environments#create-or-update-an-environment
```

#### About this error

The GitHub App token lacks permission to create environments via the REST API. This occurs when the IssueOps `request-an-env` operation runs.

#### Confirming the cause

Check which scenario applies to your setup:

1. **Missing Administration permission** (most common): The GitHub API requires `administration: write` to create environments—the `environments` permission alone is insufficient. Check your app permissions at:
   ```
   https://github.com/organizations/YOUR_ORG/settings/apps/sfops-bot
   ```
   Look for **Administration** under Repository permissions.

2. **Pending permission acceptance**: Permissions were added to the app but not yet accepted at the installation level. Check for a yellow banner at:
   ```
   https://github.com/YOUR_ORG/YOUR_REPO/settings/installations
   ```

3. **Repository not in installation scope**: If the app is installed on "Selected repositories", verify your repository is included in the list.

#### Fixing the problem

**If Administration permission is missing:**

1. Go to `https://github.com/organizations/YOUR_ORG/settings/apps/sfops-bot`
2. Click **Permissions & events**
3. Under **Repository permissions** → Set **Administration** to **Read and write**
4. Click **Save changes**
5. Accept the new permission at `https://github.com/YOUR_ORG/YOUR_REPO/settings/installations`
6. Re-run the failed workflow

**If permissions are pending acceptance:**

1. Go to `https://github.com/YOUR_ORG/YOUR_REPO/settings/installations`
2. Click **Configure** next to sfops-bot
3. Accept the pending permission request
4. Re-run the failed workflow

**If repository is not in installation scope:**

1. Go to `https://github.com/organizations/YOUR_ORG/settings/installations`
2. Click **Configure** next to sfops-bot
3. Add your repository to the selected list, or switch to **All repositories**
4. Re-run the failed workflow
