# go-dep-impact-action

A GitHub Action that performs a deep, targeted analysis of Go module dependency updates and their effects on your project.

This tool is in an early release state. We provide free hosted usage to Open Source projects which have an Apache 2.0, BSD-2-Clause, BSD-3-Clause, or MIT license.

**Usage Note:** The action runs for the entire duration of the analysis, which may take minutes to an hour. This means your GitHub Actions usage minutes will be consumed while polling for status. As an alternative, consider installing our [GitHub App go-dep-impact-app](https://github.com/PatchLens/go-dep-impact-app).

## Process

This tool is currently provided as a service, with this action conducting the necessary API calls to deliver the analysis results. The provided `GITHUB_TOKEN` is used to validate the repository specifications. The repository must be public, and unit tests must be able to run without any network access to utilize our tool in the current early release state.

You can read details about our [methodology on our website](https://patchlens.com/methodology). At a high level, our tool evaluates the risk of a dependency update by:

* Precisely identifying what has changed in updated Go dependencies
* Static analysis to map how your code interacts with those changes
* Behavior and field state analysis of your running application before and after the update

Once testing is complete, we evaluate the confidence of the analysis by introducing controlled mutations in the dependency and ensuring your expanded tests catch them. By introducing bugs in the same places the module changed, we can perform mutation testing with higher signal and at a faster speed than typically found.

## Why use this Action?

* **Catch regressions early**
  Automatically surface breaking changes or behavioral shifts before merging or releasing.
* **Speed up reviews**
  Provide clear, actionable reports so maintainers can focus on real issues, not guesswork.
* **Accelerate fixes**
  When updates introduce regressions, our tool helps you understand where and why faster.
* **Improve confidence**
  Know exactly which parts of your code and tests are impacted by a dependency bump.

## Usage

Include this action in any repository workflow. The composite action handles:

* Detection of `go.mod` changes
* Submission and polling of PatchLens analysis
* Posting a report comment on the PR

### Example for Dependabot PRs

Create a file at `.github/workflows/patchlens-dependabot.yml`:

```yaml
name: PatchLens on Dependabot PRs

on:
  pull_request:
    types: [opened, reopened, synchronize]

jobs:
  analyze:
    if: github.actor == 'dependabot[bot]'
    runs-on: ubuntu-latest

    steps:
      - name: Run PatchLens Go Module Update Analysis
        uses: PatchLens/go-dep-impact-action@v1
        with:
          directory: ''         # or 'projects/foo' if go.mod is in a subdirectory
          timeout_minutes: '20' # retry timeout
```

### Inputs

| Name              | Description                                               | Default | Required |
| ----------------- | --------------------------------------------------------- | ------- | -------- |
| `directory`       | Sub-directory containing `go.mod` (relative to repo root) | `''`    | false    |
| `timeout_minutes` | Timeout for submission & polling retries, in minutes      | `20`    | false    |

### Permissions

This action requires the following permissions in your workflow:

```yaml
permissions:
  contents: read          # for checkout and diff
  issues: write           # to post and delete report comments
  pull-requests: write    # to validate PR context
```

### Limitations

Our tool is still in an early phase of development. Because of this, there are several limitations that may impact your usage:

* Tests must be functional without network access ([contact us](https://patchlens.com/contact) for network support)
* Repositories must be public on GitHub, and have an approved license ([contact us](https://patchlens.com/contact) for private or enterprise support)
* `go.work` files are not fully supported; analysis must be run separately on each individual go.mod module within the workspace.
* Test memory usage is limited to 8GB
* Test disk usage is also restricted

## Terms of Service

By using this Action, you agree to our [Terms of Service](https://patchlens.com/terms-of-service).

