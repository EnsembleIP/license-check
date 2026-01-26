# Workflow Examples

This directory contains example workflows showing different ways to use the License Check action.

## Available Examples

### 1. Basic Workflow (`basic-workflow.yml`)

The simplest possible configuration using all default values.

**Use this when:**
- You want to get started quickly
- Default settings work for your project
- You're using the default skip-dependencies list

**To use:**
```bash
cp examples/basic-workflow.yml .github/workflows/license-check.yml
```

### 2. Advanced Workflow (`advanced-workflow.yml`)

A fully customized configuration with all available options.

**Use this when:**
- You need to skip custom internal packages
- You want to allow multiple license types
- You want to pin specific versions

**To use:**
```bash
cp examples/advanced-workflow.yml .github/workflows/license-check.yml
# Then edit the file to match your project's needs
```

## Customization Guide

### Skip Dependencies

List packages that should be excluded from license checking:

```yaml
with:
  skip-dependencies: "pkg1 pkg2 pkg3"
```

**Common use cases:**
- Internal/proprietary packages
- Packages with custom licenses
- Packages that cause false positives

### Allowed Licenses

Specify which licenses are acceptable:

```yaml
with:
  allowed-licenses: "MIT,Apache-2.0,BSD-3-Clause"
```

**Common license sets:**

**Permissive only:**
```yaml
allowed-licenses: "MIT,Apache-2.0,BSD-2-Clause,BSD-3-Clause,ISC"
```

**Permissive + Copyleft:**
```yaml
allowed-licenses: "MIT,Apache-2.0,BSD-3-Clause,GPL-3.0,LGPL-3.0"
```

**Include Proprietary:**
```yaml
allowed-licenses: "MIT,Apache-2.0,Proprietary"
```

### UV Groups

Include dependency groups (for projects using UV):

```yaml
with:
  uv-groups: "dev,gpu"
```

**Common groups to include:**
- `dev` - Development dependencies
- `gpu` - Packages that require are GPU

### Python Version

Specify the Python version to use:

```yaml
with:
  python-version: "3.11"
```

**Options:**
- `"3.x"` - Latest Python 3 (default)
- `"3.11"` - Specific version
- `"3.11.5"` - Exact version

### Licensecheck Version

Pin the licensecheck tool version:

```yaml
with:
  licensecheck-version: "2024.3"
```

**When to pin:**
- Production environments
- Reproducible builds
- Avoiding breaking changes

## Workflow Triggers

### Pull Requests Only (Recommended)

```yaml
on:
  pull_request:
    branches: [ main ]
```

### Pull Requests and Pushes

```yaml
on:
  pull_request:
    branches: [ main ]
  push:
    branches: [ main ]
```

### Scheduled Checks

```yaml
on:
  schedule:
    - cron: '0 0 * * 0'  # Weekly on Sunday
  pull_request:
    branches: [ main ]
```

## Required Permissions

Always include these permissions:

```yaml
permissions:
  contents: read
  pull-requests: write
```

- `contents: read` - To checkout the repository
- `pull-requests: write` - To post comments on PRs

## Multiple Jobs Example

Run license check alongside other checks:

```yaml
jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run linter
        run: # your lint command

  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run tests
        run: # your test command

  license-check:
    runs-on: ubuntu-latest
    needs: [lint, test]  # Run after lint and test
    steps:
      - uses: actions/checkout@v4
      - uses: ensembleip/license-check@v1
```

## Conditional Execution

### Only on PRs to Main

```yaml
if: github.event_name == 'pull_request' && github.base_ref == 'main'
```

### Skip for Dependabot PRs

```yaml
if: github.actor != 'dependabot[bot]'
```

### Only on Specific File Changes

```yaml
on:
  pull_request:
    paths:
      - 'requirements.txt'
      - 'pyproject.toml'
      - 'setup.py'
```

## Troubleshooting

### Action not running

Check:
1. Workflow file is in `.github/workflows/`
2. Workflow file has `.yml` or `.yaml` extension
3. Permissions are set correctly
4. Trigger conditions are met

### No PR comment

Check:
1. `pull-requests: write` permission is set
2. Action is running on a pull request event
3. Check workflow logs for errors

### License check fails

Check:
1. Repository is checked out before running action
2. Dependency files exist (requirements.txt, pyproject.toml, etc.)
3. Python version is compatible with your dependencies
4. Skip-dependencies list is formatted correctly (space-separated)

## More Help

- See the main [README.md](../README.md) for detailed documentation
- Check [QUICKSTART.md](../QUICKSTART.md) for setup guide
- Review [CONTRIBUTING.md](../CONTRIBUTING.md) for development info
