# Overview

**sfops** is the definitive GitHub-native implementation of the [Flxbl framework](https://flxbl.io) for Salesforce. It delivers production-ready workflows, IssueOps automation, and developer-centric tooling that you can deploy immediately—no need to build your own CI/CD or wrestle with GitHub Actions from scratch.

As the official workflow engine for Flxbl, sfops provides battle-tested, opinionated workflows that handle the complexity of Salesforce ALM on GitHub. Instead of reinventing the wheel with custom actions and workflows, you get proven patterns that work out of the box—refined through real-world enterprise implementations.

## Why sfops?

sfops delivers battle-tested Salesforce DevOps so you don't have to build it yourself:

- **Ready to deploy**: Complete workflows for builds, validations, releases, and environment management—no custom GitHub Actions required
- **Proven patterns**: Opinionated workflows refined through enterprise production use, not experimental configurations you have to debug
- **GitHub-native by design**: Everything runs on GitHub Actions, GitHub Pages, and GitHub APIs—leverage what you already have without external tools
- **Developer-first**: Built for daily development workflows—PR validation, review environments, automated testing—ready to use immediately

Built on [sfp](https://docs.flxbl.io/sfp)—the package orchestration engine powering Flxbl—sfops gives you production-grade Salesforce DevOps on GitHub without the months of effort to build it yourself.

### Architecture: GitHub Actions + sfp server

sfops runs entirely on GitHub Actions with [sfp server](https://docs.flxbl.io/sfp-server) as a stateful enhancement layer. GitHub Actions orchestrates all workflows, while sfp server provides the persistent state and coordination capabilities that GitHub's ephemeral workflow model cannot natively support.

**What sfp server provides:**
- **Persistent environment state**: Stores environment metadata, assignments, and pool state across workflow runs
- **Distributed coordination**: Enables safe concurrent access to shared environments across parallel workflows
- **Cross-workflow context**: Shares state and metadata across multiple workflows and repositories
- **Centralized authentication**: Manages credentials and tokens without exposing them to workflow logs

This architecture keeps the control plane on GitHub while adding the statefulness required for enterprise-scale Salesforce DevOps.

### AI-Powered Code Intelligence

sfops integrates AI directly into pull request workflows, providing intelligent architectural insights without blocking development. The AI PR linter automatically analyzes changed files during validation, offering:

- **Architectural pattern analysis**: Validates alignment with Flxbl framework best practices
- **Code quality insights**: Identifies potential improvements with severity-based recommendations (info, warning, concern)
- **Non-blocking feedback**: Generates GitHub check annotations that inform without failing builds
- **Contextual awareness**: Focuses analysis on changed files (up to 10 files per PR)

Supports multiple LLM providers including Anthropic Claude (recommended), OpenAI, Amazon Bedrock, and GitHub Copilot. AI features are configurable and never interrupt your workflow—insights appear as GitHub annotations alongside your standard PR checks.

## The Flxbl Way, GitHub Edition

sfops implements the complete Flxbl framework using GitHub's native platform capabilities:

- **Environments** → GitHub Environments + Salesforce Sandboxes/Scratch Orgs
- **Pipelines** → GitHub Actions Workflows with opinionated stages
- **Operations** → IssueOps powered by GitHub Issues and Forms
- **Visibility** → Dev Central on GitHub Pages
- **Collaboration** → GitHub Pull Requests + Review Sandboxes + PR Commands

This isn't just integration—it's a native GitHub experience purpose-built for Salesforce teams following Flxbl practices.

### IssueOps

sfops brings IssueOps to Salesforce development, where operational requests are raised as GitHub issues and executed by automation. Issues are picked up by workflows with a human-in-the-loop safety model—if automation encounters complexity or failure, your Salesforce admin can intervene. The result: development operations that just work.

{% embed url="https://murf.ai/embeds/index.html?embedId=lvhnr1b3" %}

### Dev Central

Dev Central is your single-pane-of-glass control center, providing complete visibility across work items, pull requests, environments, and releases. Developers can monitor builds, track deployments, view metrics, and raise operational requests—all from one unified GitHub Pages application.

{% embed url="https://app.arcade.software/share/UFVVxLpsV4dXAIcDFWs9" %}
sfops dev central
{% endembed %}
