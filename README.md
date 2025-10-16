# go-dep-impact-action

A GitHub Action for automated risk analysis of Go module dependency updates using the open-source [PatchLens](https://github.com/PatchLens/go-update-lens) tool.

This action downloads and runs PatchLens binaries directly in your GitHub Actions workflow, providing dependency analysis without external service dependencies.

> **Usage Note:**  
> Analysis duration may range from several minutes to over an hour. Be mindful of your actions usage.

---

## How PatchLens Works

This action downloads and runs the open-source PatchLens binaries directly in your GitHub Actions environment. The analysis happens locally on your runner without sending data to external services.

You can read details about our [methodology on our website](https://patchlens.com/methodology). At a high level, our tool evaluates the risk of a dependency update by:

* Precisely identifying what has changed in updated Go dependencies
* Static analysis to map how your code interacts with those changes
* Behavior and field state analysis of your running application before and after the update

Once testing is complete, we evaluate the confidence of the analysis by introducing controlled mutations in the dependency and ensuring your expanded tests catch them. By introducing bugs in the same places the module changed, we can get higher signal and at a faster speed.

---

## Why use PatchLens?

* **Catch regressions early**  
  Automatically surface breaking changes or behavioral shifts before merging or releasing.
* **Speed up reviews**  
  Provide clear, actionable reports so maintainers can focus on real issues, not guesswork.
* **Faster debugging**  
  Field-level insights point directly to the root cause, reducing investigation time.
* **Improve confidence**  
  Know exactly which parts of your code and tests are impacted by a dependency bump.

Learn more at the [PatchLens repository](https://github.com/PatchLens/go-update-lens).

---

## Usage

### Basic Setup

```yaml
name: PatchLens Analysis

on:
  pull_request:
    types: [opened, reopened, synchronize]

permissions:
  contents: write
  issues: write

jobs:
  analyze:
    runs-on: ubuntu-latest
    steps:
      - uses: PatchLens/go-dep-impact-action@v0.2.0
```

### Dependabot Integration

```yaml
jobs:
  analyze:
    if: github.actor == 'dependabot[bot]'
    runs-on: ubuntu-latest
    steps:
      - uses: PatchLens/go-dep-impact-action@v0.2.0
        with:
          directory: 'services/api'  # if go.mod is in subdirectory
```

### Inputs

| Name           | Description                          | Default       | Required |
| -------------- | ------------------------------------ | ------------- | -------- |
| `directory`    | Subdirectory containing `go.mod`     | `''` (root)   | false    |
| `github_token` | GitHub token for authentication      | `github.token` | false    |

---

## Features

- **Automatic platform detection** - Works on Linux and macOS runners (amd64, arm64)
- **Binary caching** - Downloads cached between runs for faster execution
- **Visual reports** - Charts and detailed analysis posted as PR comments
- **Private repository support** - Works with private Go modules via GitHub token

---

## Output

The action generates:
- **PR Comments** with analysis summary and visual charts
- **JSON Reports** with detailed findings stored in `patchlens-assets` branch
- **Performance metrics** showing impact of dependency changes

Analysis runs only when `go.mod` files are modified and posts results directly to your pull request.
