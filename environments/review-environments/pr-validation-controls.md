# PR Validation Controls

When creating a pull request, sfops automatically validates your changes in a review environment. You can control the validation behavior by adding special commands to your PR description.

## Available Commands

Add these commands anywhere in your PR description to control how validation is performed:


| Command             | Description                                            |
| --------------------- | -------------------------------------------------------- |
| `/sfops fast`       | Enable individual validation mode (diff-based, faster) |
| `/sfops individual` | Enable individual validation mode (diff-based, faster) |
| `/sfops quick`      | Enable individual validation mode (diff-based, faster) |
| `/sfops skiptests`  | Skip Apex test execution during validation             |

## Validation Modes

sfops supports two validation modes:

### Thorough Validation (Default)

This is the default validation mode used for all PRs. It:

- Synchronizes the entire org state with the release definition
- Validates all packages defined in the release config
- Runs all associated Apex tests
- Provides comprehensive validation but takes longer

### Individual Validation

Enabled by using `/sfops fast`, `/sfops individual`, or `/sfops quick`. This mode:

- Only validates packages that have changed in the PR
- Skips full org synchronization
- Uses Git history to determine the diff between base and head commits
- Significantly faster than thorough validation

**When to use Individual Validation:**

- Structural refactoring with no functional changes
- Metadata reorganization or renaming
- Changes to a small subset of packages
- During development iterations requiring faster feedback

## Test Control

Use `/sfops skiptests` to skip Apex test execution during validation. This adds the `--skipTesting` flag to the `sfp validate` command.

**When to skip tests:**

- Metadata-only changes that don't affect Apex code
- Configuration changes
- Combined with individual validation for structural refactors

{% hint style="warning" %}
Skipping tests reduces validation coverage. Ensure tests are run before merging to production.
{% endhint %}

## Example PR Description

```markdown
## Summary
Refactoring account management package structure - moving classes to new folders for better organization.

## Changes
- Reorganized Apex classes in the Account package
- Updated package descriptor

## Testing
Structural changes only, no functional modifications.

/sfops fast
/sfops skiptests
```

In this example, the PR will use individual validation mode and skip test execution, resulting in a much faster validation cycle for structural changes.
