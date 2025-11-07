# Security Policy

## Overview

sfops is a collection of reusable GitHub Actions and workflows for Salesforce CI/CD operations. This is an internal source repository that customers fork for their implementations. This repository handles sensitive operations including authentication to Salesforce orgs, deployment of code, and management of development environments.

### Security Considerations&#x20;

#### 1. GitHub Actions Security

This repository contains reusable workflows that execute in customer environments. Key security considerations include:

* **Secrets Management**: Workflows handle sensitive credentials (DEVHUB\_SFDX\_AUTH, GitHub tokens, NPM tokens)
* **Input Validation**: All workflow inputs should be validated and sanitized
* **Privilege Escalation**: Actions must not allow unauthorized access to Salesforce orgs
* **Supply Chain**: Dependencies in actions/ directory must be regularly updated

#### 2. Salesforce Authentication

Critical security areas:

* **SFDX Auth URLs**: Must never be logged or exposed in workflow outputs
* **DevHub Credentials**: Protected and only accessible to authorized workflows
* **Org Access**: Proper isolation between different environments (dev, staging, production)
* **Token Rotation**: Support for regular credential rotation

#### 3.  Dev Central

The `/dashboards` folder contains an internal Jekyll static site that:

* Is protected by GitHub authentication
* Displays internal project metrics and deployment status
* Only accessible to authenticated organization members
* Processes data from GitHub Actions and Salesforce deployments

### Reporting a Vulnerability

#### For Security Vulnerabilities

**Please DO NOT report security vulnerabilities through public GitHub issues on this repository.**

Since this is an internal source repository, please report security issues to:

1. **Primary Channel**: Report issues at https://source.flxbl.io repositories
2. **For Customers**: If you've forked this repository and found a security issue, report it through your organization's security channels first
3. **Critical Issues**: For critical vulnerabilities affecting the platform, contact the Flxbl security team directly

#### What to Include in Your Report

Please include:

* Description of the vulnerability
* Steps to reproduce the issue
* Potential impact assessment
* Affected versions/components
* Any proof-of-concept code (if applicable)
* Your recommended fix (if you have one)

#### Response Timeline and Severity Definitions

**Critical**: Vulnerabilities in this GitHub Actions repository that could:

* **Expose customer secrets**: Workflows that could leak GitHub secrets, SFDX auth URLs, or NPM tokens to logs or unauthorized parties
* **Compromise customer's Salesforce production orgs**: Actions that could allow unauthorized deployments or data access to production
* **Allow repository takeover**: Vulnerabilities that could let attackers modify customer's workflow files or gain write access
* **Enable supply chain attacks**: Malicious code injection that would affect all customers using these workflows
* **Resolution Target**: 7 days

**High**: Vulnerabilities that could:

* **Affect customer's CI/CD pipeline**: Break or manipulate the build/deploy process
* **Access non-production Salesforce orgs**: Unauthorized access to dev/staging environments
* **Bypass PR validation checks**: Allow untested code to reach main branches
* **Expose internal repository data**: Leak branch strategies, release definitions, or deployment configurations
* **Resolution Target**: 14 days

**Medium**: Vulnerabilities that could:

* **Cause workflow failures**: DoS that prevents workflows from running (but doesn't expose data)
* **Require repository write access**: Issues that need existing compromised credentials to exploit
* **Affect development workflows only**: Issues in scratch org creation or sandbox management
* **Impact internal tooling**: Issues in the dashboard that only affect authenticated internal users
* **Resolution Target**: 30 days

**Low**: Vulnerabilities that:

* **Are mitigated by GitHub's security model**: Issues that GitHub's runner isolation already prevents
* **Require admin access to exploit**: Problems that need repository admin permissions
* **Only affect optional features**: Issues in non-critical actions or utilities
* **Have no security impact in practice**: Theoretical vulnerabilities in the runner context
* **Resolution Target**: 90 days

#### For Forked Repositories

If you fork this repository:

1. Review all workflow files before enabling GitHub Actions
2. Update Docker image references to your own registry
3. Audit all custom actions in the actions/ directory
4. Ensure proper secret management in your fork
5. Keep your fork updated with security patches

### Known Security Considerations

#### False Positive CVEs

Many CVEs reported by Dependabot for this repository are false positives because:

* GitHub Actions run in isolated, ephemeral environments
* No persistent server infrastructure
* No direct user input processing
* Controlled execution context

Focus on CVEs that affect:

* Build-time security
* Secret exposure risks
* Supply chain vulnerabilities
* Authentication/authorization logic

#### Dependency Management

* Development dependencies are excluded from Dependabot updates (see .github/dependabot.yml)
* Production dependencies in actions are regularly updated
* Docker base images are monitored for vulnerabilities

### Security Features

This repository implements:

* Automated dependency updates via Dependabot
* Secret scanning (if enabled in your GitHub organization)
* Code scanning for custom actions
* Minimal permission requirements in workflows
* Input validation in reusable workflows

### Contact

* **Security Issues**: Report at https://source.flxbl.io
* **General Questions**: See [documentation](https://docs.flxbl.io/sfops/)
* **Bug Reports**: Use appropriate repository at source.flxbl.io (for non-security bugs only)

### Acknowledgments

We appreciate responsible disclosure and will acknowledge security researchers who help improve sfops security (with their permission).

***

_This security policy is regularly reviewed and updated. Last update: August 2025_
