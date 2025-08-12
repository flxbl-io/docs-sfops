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

* Navigate to your GitHub organization's settings.
* Click on "Developer settings" and select "GitHub Apps".
* Hit "New GitHub App" and configure:
  - **GitHub App name**: `sfops-bot`
  - **Homepage URL**: `https://github.com/YOUR_ORG/sfops` (or your organization URL)
  - **Webhook**: Uncheck "Active" (webhooks are not needed for sfops-bot)
* Add an icon and background color in the 'Display Information' to make the app identifiable in your workflows

### **Step 2: Permissions Configuration**

Configure the following permissions for the sfops-bot app:

#### Repository Permissions
| Permission        | Access Level | Purpose                                                                                                                                        |
|-------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
| **Actions**       | Read & Write | Crucial for the app to manage GitHub Actions, which are integral to automation workflows                                                       |
| **Contents**      | Read & Write | Manage code, branches, commits, and merges. This access allows the app to automate code integration processes                                  |
| **Deployments**   | Read & Write | Empowers the app to manage deployments, essential for continuous delivery workflows                                                            |
| **Environments**  | Read & Write | Create environments, which will be consumed by workflows                                                                                       |
| **Issues**        | Read & Write | Enable the app to automate issue tracking, commenting, and labeling                                                                            |
| **Projects**      | Read & Write | Allow the app to connect issues to projects for better project management                                                                      |
| **Pull Requests** | Read & Write | Necessary for the app to automate the handling of pull requests, including merging and labeling                                                |
| **Secrets**       | Read         | Lets the app manage secrets without compromising their security, essential for secure workflows                                                |
| **Variables**     | Read & Write | Permit the app to read the variables in the repo. This is vital for dynamic configuration of the environment and branch related configurations |
| **Workflows**     | Read & Write | Permit the app to update workflow files, which is vital for maintaining automated processes                                                    |

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

### **Step 6: Collect App Credentials and Store as Secrets**

#### Collect the required credentials (from Step 4's settings page):
1. **App ID**: Note the App ID displayed at the top of your app's settings page
2. **Private Key**: 
   - Scroll down to the **Private keys** section on your app's settings page
   - Click **"Generate a private key"**
   - The `.pem` file will download automatically - save it securely

#### Store as GitHub secrets:
* Navigate to your `sfops` repository → **Settings → Secrets and variables → Actions**
* Choose where to create the secrets:
  - **Organization secrets** (recommended): Available to all repositories in your org
  - **Repository secrets**: Scoped to just the `sfops` repository
* Create two secrets:
  - **`SFOPSBOT_APP_ID`**: Paste the App ID number from the settings page
  - **`SFOPSBOT_APP_PRIVATE_KEY`**: Paste the entire contents of the downloaded `.pem` file

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
