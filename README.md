# dependabot-sim

Reusable GitHub Actions workflow for testing Dependabot configurations locally.

## Features

- 🔄 Simulates Dependabot updates locally using the Dependabot CLI
- 📦 Supports all package ecosystems (npm, pip, bundler, go-modules, docker, github-actions, etc.)
- 🔍 Shows which dependencies would be updated without creating PRs
- ⚙️ Configurable Node.js and Go versions
- 📊 Uploads job and output files as artifacts for inspection
- 🎯 Test your `dependabot.yml` configuration before deploying

## Usage

### Basic Usage

Add this workflow to your repository at `.github/workflows/test-dependabot.yml`:

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
  test:
    uses: festum/dependabot-sim/.github/workflows/dependabot-test.yml@v1
```

### Advanced Usage with Custom Inputs

```yaml
name: Test Dependabot Configuration

on:
  workflow_dispatch:

jobs:
  test:
    uses: festum/dependabot-sim/.github/workflows/dependabot-test.yml@v1
    with:
      node-version: '20'
      go-version: '1.21'
      dependabot-config-path: '.github/dependabot.yml'
```

### Using Outputs

```yaml
jobs:
  test:
    uses: festum/dependabot-sim/.github/workflows/dependabot-test.yml@v1

  show-results:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - name: Display summary
        run: |
          echo "${{ needs.test.outputs.result-summary }}"
```

## Inputs

| Name | Description | Default | Required |
|------|-------------|---------|----------|
| `node-version` | Node.js version to use | `'20'` | No |
| `go-version` | Go version to use | `'1.21'` | No |
| `dependabot-config-path` | Path to dependabot.yml file | `'.github/dependabot.yml'` | No |

## Outputs

| Name | Description |
|------|-------------|
| `result-summary` | Summary of Dependabot test results |

## How It Works

1. Checks out your repository
2. Reads your `.github/dependabot.yml` configuration
3. Converts the configuration to Dependabot CLI format
4. Runs Dependabot CLI simulations for each package ecosystem
5. Parses the output and displays which packages would be updated
6. Uploads job files and output files as artifacts

## Example Output

```
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
- The workflow requires public repository access (or appropriate permissions for private repos)

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

This workflow uses the official [Dependabot CLI](https://github.com/dependabot/cli) to simulate updates.

## Related

- [Dependabot Documentation](https://docs.github.com/en/code-security/dependabot)
- [Dependabot CLI](https://github.com/dependabot/cli)
- [Configuring Dependabot](https://docs.github.com/en/code-security/dependabot/dependabot-version-updates/configuration-options-for-the-dependabot.yml-file)
