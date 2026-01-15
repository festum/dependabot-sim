# dependabot-sim

GitHub Action for simulating Dependabot updates locally.

## Features

- 🔄 Simulates Dependabot updates locally using the Dependabot CLI
- 📦 Supports all package ecosystems (npm, pip, bundler, go-modules, docker, github-actions, etc.)
- 🔍 Shows which dependencies would be updated without creating PRs
- 📊 Uploads job and output files as artifacts for inspection
- 🎯 Test your `dependabot.yml` configuration before deploying

## Usage

### Basic Usage

Add this to your workflow at `.github/workflows/test-dependabot.yml`:

```yaml
name: Test Dependabot Configuration

on:
  pull_request:
    paths:
      - '.github/dependabot.yml'
  push:
    branches:
      - main
  workflow_dispatch:

jobs:
  simulate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v6
      - uses: festum/dependabot-sim@v1
```

### Custom Dependabot Config Path

If your dependabot.yml is in a non-standard location:

```yaml
steps:
  - uses: actions/checkout@v6
  - uses: festum/dependabot-sim@v1
    with:
      dependabot-config-path: '.github/custom-dependabot.yml'
```

### Using Outputs

```yaml
jobs:
  simulate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v6

      - uses: festum/dependabot-sim@v1
        id: dependabot

      - name: Display summary
        run: |
          echo "${{ steps.dependabot.outputs.summary }}"
```

## Inputs

| Name | Description | Default | Required |
|------|-------------|---------|----------|
| `dependabot-config-path` | Path to dependabot.yml file | `.github/dependabot.yml` | No |

## Outputs

| Name | Description |
|------|-------------|
| `summary` | Summary of Dependabot simulation results |

## How It Works

1. Sets up Node.js 20 and Go 1.21
2. Reads your `.github/dependabot.yml` configuration
3. Converts the configuration to Dependabot CLI format
4. Runs Dependabot CLI simulations for each package ecosystem
5. Parses the output and displays which packages would be updated
6. Uploads job files and output files as artifacts

## Example Output

```log
## Packages that would be updated

### npm
- @types/node: 18.0.0 → 20.10.0
- typescript: 4.9.0 → 5.3.3
- eslint: 8.50.0 → 8.56.0

### github-actions
- actions/checkout: v3 → v4
- actions/setup-node: v3 → v4
```

## Requirements

- Your repository must have a `.github/dependabot.yml` file
- You must checkout your repository before using this action

## Example Dependabot Configuration

```yaml
version: 2
updates:
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "weekly"
    groups:
      dev-dependencies:
        patterns:
          - "@types/*"
          - "eslint*"
    ignore:
      - dependency-name: "*"
        update-types: ["version-update:semver-patch"]

  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      interval: "weekly"
```

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

MIT License - see [LICENSE](LICENSE) file for details.

## Credits

This action uses the official [Dependabot CLI](https://github.com/dependabot/cli) to simulate updates.

## Related

- [Dependabot Documentation](https://docs.github.com/en/code-security/dependabot)
- [Dependabot CLI](https://github.com/dependabot/cli)
- [Configuring Dependabot](https://docs.github.com/en/code-security/dependabot/dependabot-version-updates/configuration-options-for-the-dependabot.yml-file)
