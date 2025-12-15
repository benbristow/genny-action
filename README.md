# Genny Action

[![Test Action](https://github.com/benbristow/genny-action/actions/workflows/test.yml/badge.svg)](https://github.com/benbristow/genny-action/actions/workflows/test.yml)

A GitHub Action that clones the [Genny](https://github.com/benbristow/genny) static site generator repository, compiles it with .NET 10 SDK, and runs it with the `build --verbose` command to generate your static site.

## Usage

### Basic Usage

The action will clone Genny, build it, and run it from your repository's root directory (or specified working directory):

```yaml
name: Build with Genny
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      
      - name: Build site with Genny
        uses: benbristow/genny-action@v1
```

### With Custom Working Directory

If your Genny site files are in a subdirectory:

```yaml
- name: Build site with Genny
  uses: benbristow/genny-action@v1
  with:
    working-directory: './site'
```

### With Outputs

Access the build directory output:

```yaml
- name: Build site with Genny
  id: genny
  uses: benbristow/genny-action@v1

- name: Display build directory
  run: |
    echo "Build directory: ${{ steps.genny.outputs.build-directory }}"

- name: Deploy site
  run: |
    # Use the build directory for deployment
    rsync -av "${{ steps.genny.outputs.build-directory }}/" deploy-target/
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `repository` | Repository URL to clone | No | `https://github.com/benbristow/genny` |
| `working-directory` | Working directory for the action | No | `.` |

## Outputs

| Output | Description |
|--------|-------------|
| `build-directory` | Directory where the site was built |

## What This Action Does

1. **Clones the Genny repository** from the specified URL (defaults to `https://github.com/benbristow/genny`)
2. **Compiles the Genny project** using .NET 10 SDK
3. **Runs Genny** with the `build --verbose` command from the working directory to generate the static site

**Important:** Genny runs from the working directory, so make sure your working directory contains your Genny site structure. The generated site will be created in `build/` within the working directory.


## Requirements

- Git must be available for cloning the repository
- .NET 10 SDK is automatically set up by the action using `actions/setup-dotnet@v4`

## Development

This is a composite action using bash. No build step is required - the `action.yml` file contains the bash script directly.

### Testing

The repository includes a `demosite/` directory that can be used for testing the action locally or in CI. The test workflow (`.github/workflows/test.yml`) demonstrates how to use the action with the demosite.

## License

This project is licensed under the GPL-3.0 License or greater - see the [LICENSE](LICENSE) file for details.

