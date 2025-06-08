# go-dep-impact-action

A GitHub Action for deep, automated risk analysis of Go module dependency updates, and their effects on your project.

This tool is in an early release state. We provide free hosted usage to Open Source projects which have a declared Apache 2.0, BSD-2-Clause, BSD-3-Clause, or MIT license.

> **Usage Note:**  
> The action runs for the full analysis duration, which may range from several minutes to over an hour. GitHub Actions usage minutes are consumed during status polling. For optimized workflows, consider our [GitHub App go-dep-impact-app](https://github.com/PatchLens/go-dep-impact-app).

---

## How PatchLens Works

This action interfaces with the PatchLens service, submitting your repository for deep static and dynamic analysis through our API. Your `GITHUB_TOKEN` is used for authentication. The repository must be public, and all tests must run without network access for analysis to succeed (see [limitations](#limitations)).

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

---

## Usage

Add this Action to your workflow to analyze Go module changes. The action:

* Detects `go.mod` changes on pull requests
* Submits the PatchLens analysis job and polls for results
* Posts a clear, actionable report as a comment on your PR

### Example: Analyze Dependabot PRs

Create a file at `.github/workflows/patchlens-dependabot.yml`:

```yaml
name: PatchLens on Dependabot PRs

on:
  pull_request:
    types: [opened, reopened, synchronize]

permissions:
  contents: write
  issues: write
  pull-requests: write

jobs:
  analyze:
    if: github.actor == 'dependabot[bot]'
    runs-on: ubuntu-latest

    steps:
      - name: Run PatchLens Go Module Update Analysis
        uses: PatchLens/go-dep-impact-action@v1
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          directory: ''         # or 'projects/foo' if go.mod is in a subdirectory
          timeout_minutes: '20' # retry timeout
```

### Inputs

| Name              | Description                                               | Default | Required |
| ----------------- | --------------------------------------------------------- | ------- | -------- |
| `directory`       | Sub-directory containing `go.mod` (relative to repo root) | `''`    | false    |
| `timeout_minutes` | Timeout for submission & polling retries, in minutes      | `20`    | false    |
| `github_token`    | Set to ${{ secrets.GITHUB_TOKEN }} for authentication     | `''`    | true     |

### Limitations

Our tool is still in an early phase of development. Because of this, there are several limitations that may impact your usage:

* Tests must be functional without network access ([contact us](https://patchlens.com/contact) for network support)
* Repositories must be public on GitHub, and have an approved license ([contact us](https://patchlens.com/contact) for private or enterprise support)
* `go.work` files are not fully supported; analysis must be run separately on each individual go.mod module within the workspace
* Unit tests must be fast enough to complete within 30 seconds per-test
* Reflection and network requests are not able to be analyzed, tests dependent on reflection or RPC may not be detected to be relevant for module changes
* Field size limits exist, currently configured to recurse 20 fields deep and examine slices up to the first 1000 entries
* Test memory usage is limited to 8GB (including needed overhead for our tool)
* Test disk usage is also restricted

### Assets

On a successful analysis, the branch `patchlens-assets` will be created. This branch will hold the reports from the analysis to reference in the comments.

## Terms of Service

By using this Action, you agree to our "as is" [Terms of Service](https://patchlens.com/terms-of-service) and [Privacy Policy](https://patchlens.com/privacy-policy).
