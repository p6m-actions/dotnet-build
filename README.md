# Dotnet Build

![Latest Release](https://img.shields.io/github/v/release/p6m-actions/dotnet-build?style=flat-square&label=Latest%20Release&color=blue)

## Description

A composite GitHub Action for building .NET applications with comprehensive testing and artifact generation capabilities. This action is designed to be part of a suite of reusable GitHub Actions for .NET development workflows, alongside `dotnet-cut-tag` and `dotnet-repository-login`.

The action provides a standardized way to:
- Set up the .NET SDK
- Restore NuGet dependencies
- Build .NET solutions/projects
- Run unit and integration tests
- Collect code coverage (optional)
- Publish build artifacts (optional)

## Usage

```yaml
steps:
  - uses: actions/checkout@v4
  
  - name: Build .NET Application
    uses: p6m-actions/dotnet-build@v1
    with:
      dotnet-version: '8.0'
      solution-path: './src/MyApplication.sln'
      configuration: 'Release'
      run-tests: true
      collect-coverage: true
      publish-artifacts: true
      artifact-name: 'my-app-artifacts'
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `dotnet-version` | The .NET version to use for building | No | `8.0` |
| `solution-path` | Path to the solution file or directory containing the solution | No | `.` |
| `configuration` | Build configuration (Debug or Release) | No | `Release` |
| `run-tests` | Whether to run tests during build | No | `true` |
| `collect-coverage` | Whether to collect code coverage | No | `false` |
| `publish-artifacts` | Whether to publish build artifacts | No | `false` |
| `artifact-name` | Name for the published artifacts | No | `dotnet-build-artifacts` |
| `verbosity` | MSBuild verbosity level | No | `minimal` |

## Outputs

| Output | Description |
|--------|-------------|
| `build-version` | The version of the built application |
| `test-results` | Test execution results summary |
| `coverage-report` | Code coverage percentage if collected |

## Examples

### Basic Build

```yaml
name: CI Build

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Build Application
        uses: p6m-actions/dotnet-build@v1
```

### Build with Testing and Coverage

```yaml
name: CI Build with Testing

on:
  push:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Build and Test
        uses: p6m-actions/dotnet-build@v1
        with:
          dotnet-version: '8.0'
          configuration: 'Release'
          run-tests: true
          collect-coverage: true
          verbosity: 'normal'
      
      - name: Display Results
        run: |
          echo "Build Version: ${{ steps.build.outputs.build-version }}"
          echo "Test Results: ${{ steps.build.outputs.test-results }}"
          echo "Coverage: ${{ steps.build.outputs.coverage-report }}"
```

### Build with Artifact Publishing

```yaml
name: Release Build

on:
  push:
    tags: [ 'v*' ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Build and Publish
        uses: p6m-actions/dotnet-build@v1
        with:
          dotnet-version: '8.0'
          solution-path: './src/MyApp.sln'
          configuration: 'Release'
          run-tests: true
          publish-artifacts: true
          artifact-name: 'release-artifacts'
```

### Multi-Framework Build

```yaml
name: Multi-Framework Build

on:
  push:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        dotnet-version: ['6.0', '7.0', '8.0']
    steps:
      - uses: actions/checkout@v4
      
      - name: Build with .NET ${{ matrix.dotnet-version }}
        uses: p6m-actions/dotnet-build@v1
        with:
          dotnet-version: ${{ matrix.dotnet-version }}
          run-tests: true
```