# go-dep-impact-action

A GitHub Action that performs a deep, targeted analysis of Go module dependency updates and their effects on your project.

This tool is in an early release state. We are providing free hosted usage to Open Source projects which have an Apache2, BSD-2, BSD-3, or MIT license.

## Process

This tool is currently provided as a service, with this action conducting the needed API calls to provide the analysis results. The provided GITHUB_TOKEN is used to validate the repo specifications. A repo must be public, and unit tests must be able to run without any network access to be able to utilize our tool in the current early release state.

The safety and confidence for a given update is determined through:

### 1. Analysis
Our tool begins by analyzing both dependency changes and their impact on your project. This includes:
- Precisely identifying what has changed in updated golang dependencies
- Static analysis to map how your code interacts with those changes
- Static analysis of existing unit tests as a starting point for validation

### 2. Expand
After understanding dependency changes and project interactions, we expand testing coverage to validate stability:
- Automatically identifying points in your code where field values should be monitored
- Extending tests to observe deep field or behavior changes anywhere in the project—not just where test assertions already exist

### 3. Validate
Once prepared, the tool validates against the intersection of dependency changes and your code:
- Detecting any obvious test failures and tracing where the behavior change originated
- Checking for internal field or behavior changes at call‑path intersections with updated module functions
- Measuring confidence by introducing controlled mutations in the dependency and ensuring your expanded tests catch them

## Why use this Action?

- **Catch regressions early**  
  Automatically surface breaking changes or behavioral shifts before merging or releasing.
- **Speed up reviews**  
  Provides clear, actionable reports so maintainers can focus on real issues, not guesswork.  
- **Accelerate Fixes**  
  When updates introduce regressions, our tool helps you understand where and why faster.
- **Improve confidence**  
  Know exactly which parts of your code and tests are impacted by a dependency bump.

## Terms of Service

By using this Action, you agree to our [Terms of Service](https://patchlens.com/terms-of-service).
