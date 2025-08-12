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
* Hit "New GitHub App" and input `sfops-bot` as the name.
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

### **Step 3: Generate and Secure a Private Key**

* In the 'General' section of your app's settings, locate the 'Private keys' subsection.
* Click on "Generate a private key" and download the `.pem` file immediately to your secure location.

### **Step 4: Configure Installation Settings**

* In the **Where can this GitHub App be installed?** section:
  - Select **"Only on this account"** to restrict the app to your organization
  - This ensures the app can only be installed within your organization

### **Step 5: Installation of the App**

* After creating the app, navigate to the 'Install App' tab within your app settings.
* Click "Install" to initiate the installation process.
* Select your organization and choose to install the app on all repositories or specific ones such as Salesforce repositories and `sfops-dev-central`.

### **Step 6: Storing the Private Key and App ID as Secrets**

* Access your organization or repository settings and go to the 'Secrets' area.
* Choose "New repository secret" or "New organization secret" for both the private key and App ID.
* Label the private key secret as `SFOPSBOT_APP_PRIVATE_KEY` and the App ID secret as `SFOPSBOT_APP_ID`.
* Paste the contents of the private key file and the numerical App ID into their respective secrets.

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
