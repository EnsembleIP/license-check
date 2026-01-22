# License Check GitHub Action

This repo provides a reusable **GitHub Action** that validates dependency licenses in your Python project and posts the results as a comment on pull requests.

## Features

- **Validates dependency licenses** using the `licensecheck` tool
- **Posts results to PR comments** with automatic create/update behavior
- **Highly configurable** with customizable allowed licenses, skip lists, and more
- **Supports UV dependency groups** to exclude dev dependencies
- **Automatic comment management** - updates existing comments instead of creating duplicates

The action is implemented as a composite action using `actions/github-script@v7`.

## Usage

Add this to your repository workflow to check licenses on pull requests:

```yaml
name: License Check
on:
  pull_request:
    branches: [ main ]

permissions:
  contents: read
  pull-requests: write

jobs:
  license-check:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: License Check
        uses: ensembleip/license-check@v1
```

## Versioning / pinning

`uses: ensembleip/license-check@vx` requires that this repository has a **Git tag** named `vx` (or a branch named `vx`).

- For initial testing you can use `@main` or `@master`.
- For production usage, prefer pinning to a commit SHA, or use a major tag like `@v1` that you keep updated to the latest `v1.x.y`.

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `skip-dependencies` | Space-separated list of dependencies to skip during license check | No | `search_backend ipllm data-schema bql model-ranktriever majestic-tokenizer` |
| `python-version` | Python version to use for running licensecheck | No | `3.x` |
| `licensecheck-version` | Version of licensecheck to install | No | `2024.3` |
| `allowed-licenses` | Comma-separated list of allowed license types | No | `Proprietary` |
| `uv-groups` | UV dependency groups to exclude (e.g., 'dev' to exclude development dependencies) | No | `dev` |

## How It Works

The action performs the following steps:

1. **Sets up Python** using the specified version
2. **Installs licensecheck** at the specified version
3. **Runs license check** with the configured parameters:
   - Checks against allowed licenses
   - Excludes specified UV dependency groups
   - Skips specified dependencies
4. **Posts results to PR** as a comment (creates new or updates existing)

## Customization

You can customize the license check behavior with various inputs:

### Custom Skip Dependencies

Skip specific dependencies from the license check:

```yaml
- name: License Check
  uses: ensembleip/license-check@v1
  with:
    skip-dependencies: "my-internal-package another-package"
```

### Custom Allowed Licenses

Specify which licenses are allowed:

```yaml
- name: License Check
  uses: ensembleip/license-check@v1
  with:
    allowed-licenses: "MIT,Apache-2.0,BSD-3-Clause"
```

### Different Python Version

Use a specific Python version:

```yaml
- name: License Check
  uses: ensembleip/license-check@v1
  with:
    python-version: "3.11"
```

### Different Licensecheck Version

Pin to a specific licensecheck version:

```yaml
- name: License Check
  uses: ensembleip/license-check@v1
  with:
    licensecheck-version: "2024.2"
```

### Different UV Groups

Exclude different dependency groups:

```yaml
- name: License Check
  uses: ensembleip/license-check@v1
  with:
    uv-groups: "dev,test"
```

## Example PR Comment

The action posts a comment like this on pull requests:

```markdown
## License Check Results

```
✓ All dependencies have acceptable licenses.

Dependencies checked: 42
Licenses found:
  - MIT: 25 packages
  - Apache-2.0: 10 packages
  - BSD-3-Clause: 7 packages
```
```

## Permissions

The workflow using this action requires the following permissions:

- `contents: read` - to read repository contents
- `pull-requests: write` - to post comments on pull requests

The action automatically uses `github.token` provided by GitHub Actions, so no token input is required.

## Notes

- The action only posts comments on pull request events
- Comments are automatically updated if they already exist (no duplicates)
- The action uses a hidden HTML comment marker to identify its own comments
- All dependencies specified in `skip-dependencies` are excluded from validation

## Requirements

- Your repository must have Python dependencies that can be analyzed by `licensecheck`
- The action works best with projects using standard Python dependency management (pip, poetry, uv, etc.)

## Troubleshooting

### License check fails to find dependencies

Make sure you've checked out your repository before running the action:

```yaml
- name: Checkout Repository
  uses: actions/checkout@v4
```

### Custom dependency file location

If your dependencies are in a non-standard location, you may need to adjust your workflow or the licensecheck command parameters.

## Contributing

Contributions are welcome! Please feel free to submit issues or pull requests.

## License

MIT License - see [LICENSE](LICENSE) for details.
