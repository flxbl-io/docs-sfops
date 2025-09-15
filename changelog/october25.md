# October25

## 🆕 Features

### Auto-Enable Trace Logging in CI/CD
*Available from: October 2025*

sfp automatically detects when your CI/CD pipeline is running in debug mode and enables trace logging accordingly.

#### Supported CI Platforms
- **GitHub Actions**: `RUNNER_DEBUG=1`, `ACTIONS_RUNNER_DEBUG=1`, `ACTIONS_STEP_DEBUG=1`
- **GitLab CI**: `CI_DEBUG_TRACE=1`, `CI_DEBUG_SERVICES=1`
- **Azure DevOps**: `SYSTEM_DEBUG=true`, `System.Debug=true`, `system.debug=true`
- **Buildkite**: `BUILDKITE_DEBUG=1`, `BUILDKITE_AGENT_DEBUG=1`

#### Manual Override
```bash
SFP_DEBUG=1 sfp build
```

#### Example - GitHub Actions
When re-running a failed workflow, click "Re-run jobs" → "Enable debug logging":
```yaml
# No changes needed - sfp detects RUNNER_DEBUG automatically
- run: sfp validate
```