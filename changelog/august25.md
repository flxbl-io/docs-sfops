# August25

### 🆕 Functional Changes (v31.1.1 → v31.2.0)

#### 🚀 Major Feature: v3 Workflows & Enhanced Server Integration (Optional Project Workflows)

If you have provisioned sfp server, you can start utilizing these workflows for pushing workflow related data and events to sfp server.

**SFP Server v3 Support**

* **Review Environments Pool**: Introduced v3 workflows with new server-based review environments pool
* **Release Definition Storage**: Release definitions are now stored and tracked on the server for complete auditability
* **Server Publishing**: v2 workflows updated with direct server publish commands
* **Environment Outputs**: Workflows now provide release definitions and environment information as outputs for better integration

**Issue Analyzer v2**

* **Enhanced Analysis**: Completely revamped issue analyzer with v2 capabilities
* **Smarter Detection**: Improved pattern matching and issue categorization
* **Automated Timing**: Issues now close automatically after 5 minutes of resolution
* **Better Reporting**: More detailed issue reports with actionable insights

**Apex Testing Improvements**

* **Test Environment Support**: Full Apex test support on dedicated test environments
* **Metadata Filtering**: SFP environment list filtering with metadata support
* **Pool Matching**: Fixed and improved pool matching for test execution
* **Daily Quality Tests**: Resolved issues with daily quality testing workflows

#### 📦 NPM & Artifact Management

**NPM Authentication**

* **Secure Artifact Fetching**: Added NPM authentication to ensure reliable artifact retrieval
* **Private Registry Support**: Full support for private NPM registries
* **Token Management**: Secure handling of NPM tokens across workflows

#### 🏗️ Infrastructure & Performance

**Node.js 22 Upgrade**

* **Platform Update**: Upgraded all workflows from Node.js 18 to Node.js 22
* **Performance Gains**: Significant performance improvements with newer runtime
* **Security Updates**: Latest security patches and vulnerability fixes

**Workflow Optimization**

* **Faster Execution**: Removed unnecessary checkout steps in v2 workflows
* **JSON Compacting**: Optimized JSON output for GitHub Actions compatibility
* **Variable Management**: Better handling of GitHub variables as workflow inputs



### 🔄 CI/CD Workflow Changes

#### Release-Please Workflow Enhancements

The release-please workflow now includes significant improvements for managing SFOPS deployments:

**Key Changes from Previous Version:**

* **Base Image Configuration**: Now explicitly passes base image configuration parameters to sync-org workflow
  * `base_image_tag`: Defaults to 'latest' but configurable
  * `base_registry`: Configurable registry (defaults to ghcr.io)
  * `base_repo`: Configurable repository owner (defaults to flxbl-io)
  * `base_image_name` & `base_lite_image_name`: Configurable image names for flexibility
* **Skip Docker Build Option**: Added `skip_docker_build` flag to sync workflows without rebuilding Docker images
* **GitHub Variables Support**: All base configuration now supports GitHub variables with fallback defaults

#### Sync-Org Workflow Architecture

Complete redesign of organization synchronization workflow:

**Migration from repository\_dispatch to workflow\_dispatch:**

* **Direct Invocation**: Can now be triggered directly from GitHub UI with clear parameter inputs
* **Descriptive Run Names**: Shows target organization and correlation ID in workflow run title
* **Two Trigger Modes**:
  1. **workflow\_call**: For internal reuse (from release-please or other workflows)
  2. **workflow\_dispatch**: For manual triggers with full parameter control

**New Configuration Parameters:**

```yaml
# Base image configuration
base_image_tag: 'latest' or specific version
base_image_name: 'sfp-pro' or 'sfp-pro-rc'
base_lite_image_name: 'sfp-pro-lite' or 'sfp-pro-lite-rc'
base_registry: Custom registry support
base_repo: Custom repository owner
sfp_version: SFP version to use
skip_docker_build: Skip building, only sync
```

**Workflow Dispatch Parameters (for manual triggers):**

* `org_name`: Target organization
* `repo_owner`: Repository owner
* `project_repo`: Project repository name
* `sfops_action_repo`: SFOPS actions repository
* `dashboard_repo`: Dashboard repository
* `installation_token`: GitHub App installation token
* `sfp_server_url`: SFP server URL for tracking
* `correlation_id`: Unique tracking ID
* `skip_docker_build`: Optional flag to skip Docker builds

#### Sync-Single-Org Workflow

New simplified workflow for syncing a single organization:

* **Easy Manual Trigger**: Simple UI-based trigger from GitHub Actions
* **Environment-based Configuration**: Uses GitHub environment variables
* **Inherits Secrets**: Automatically inherits all repository secrets
* **Configurable via Variables**: Uses `vars.BASE_REGISTRY`, `vars.BASE_REPO`, etc.

### 🔧 Bug Fixes & Improvements

#### Critical Fixes

* **Environment Variables**: Resolved base variable configuration issues
* **Pool Matching**: Fixed incorrect output in pool matching logic
* **Daily Testing**: Resolved issues with daily quality testing workflows

#### Workflow Reliability

* **Action References**: Updated and fixed action references in workflows
* **Container Images**: Corrected container image references in static analysis
* **Repository Routing**: Fixed incorrect routing in action repositories
* **Authentication Steps**: Removed incorrect auth steps that caused failures

#### Performance Optimizations

* **Checkout Removal**: Eliminated unnecessary checkouts for faster execution
* **JSON Formatting**: Optimized JSON output for GitHub Actions
* **Variable Passing**: Fixed variable passing between workflow stages
* **Environment Handling**: Improved environment variable propagation

### 📋 How to Use the New CI/CD Workflows

#### Setting Up Organization Sync for Your SFOPS Fork

**Prerequisites**

1. Fork SFOPS repository to your GitHub organization
2. Configure GitHub Variables in your fork's settings
3. Set up required secrets

**Configure GitHub Variables**

Navigate to Settings → Secrets and variables → Actions → Variables:

```
BASE_REGISTRY=ghcr.io          # Or your custom registry
BASE_REPO=flxbl-io              # Or your organization
BASE_IMAGE_NAME=sfp-pro         # Base SFP image name
BASE_LITE_IMAGE_NAME=sfp-pro-lite  # Lite image name
ORGS=["org1","org2","org3"]    # List of orgs to sync
```

**Manual Sync Using GitHub UI**

1. Go to Actions tab in your SFOPS fork
2. Select "Sync Single Organization" workflow
3. Click "Run workflow"
4. Enter parameters:
   * `org_name`: Your target organization (must exist as GitHub environment)
   * `skip_docker_build`: Check to skip Docker build (useful for testing)
5. Click "Run workflow"

**Automated Sync on Release**

The release-please workflow automatically syncs to all configured organizations:

```yaml
# Triggered automatically on:
# - Push to main branch
# - Manual workflow dispatch
# - When release-please creates a new release
```

#### Using v3 Workflows with SFP Server (Optional)

If you have SFP Server provisioned, enable v3 features:

**Setup in Project Workflows**

```yaml
# .github/workflows/sfops-release-v3.yml
name: Release with SFP Server
uses: your-org/sfops-gh-actions/.github/workflows/release-v3.yml@main
with:
  workflow_version: "v3"
  use_server_pool: true
  store_release_definitions: true
secrets:
  SFP_SERVER_URL: ${{ secrets.SFP_SERVER_URL }}
  SFP_SERVER_TOKEN: ${{ secrets.SFP_SERVER_TOKEN }}
```

**Monitor via SFP Server**

```bash
# View stored release definitions
sfp server releases list

# Get specific release details
sfp server releases get --id <release-id>
```

### 📋 Updating project workflows to use sfp-server based workfows

#### For Existing Users

**1. Update Your Workflows**

```bash
# Pull latest workflow definitions
git pull origin main

# Update your project workflows
cp .github/workflows/sfops-*.yml /path/to/your/project/.github/workflows/
```

**2. Configure GitHub Variables**

```bash
# Set required variables in GitHub UI or CLI
gh variable set BASE_IMAGE_TAG --body "v32.0.0"
gh variable set NPM_REGISTRY --body "https://npm.pkg.github.com"
gh variable set SFP_SERVER_URL --body "https://sfp.myorg.com"
```

**3. Update Secrets**

```bash
# Add new required secrets
gh secret set SFP_SERVER_TOKEN
```

**3**
