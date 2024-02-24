---
description: Trunk Check detailed command line options
---

# CLI options

## Options

| options         |                                                                                                                                       |
| :-------------- | :------------------------------------------------------------------------------------------------------------------------------------ |
| `--all`         | Run on all the files in the repository. Useful if trying to assess a new linter in the system, or to find and fix pre-existing issues |
| `--fix`         | Auto-apply all suggested fixes                                                                                                        |
| `--no-fix`      | Surface, but do not prompt for autofixes                                                                                              |
| `--filter`      | List of comma-separated linters to run. Specify `--filter=-linter` to disable a linter.                                               |
| `--sample=N`    | Run check on a [sampling](cli-options.md#sample) of all files in the repo                                                             |
| `--ci`          | Run in [Continuous Integration mode](../../check-cloud-ci-integration/continuous-integration/readme.md)                               |
| `--no-progress` | Do not show progress while running                                                                                                    |
| `--ci-progress` | Only show progress every 30s while running (useful for CI jobs). Implied by `--ci`.                                                   |
| `--jobs`        | number of concurrent jobs (does not affect background linting)                                                                        |
| `--help`        | Output help information                                                                                                               |

## Advanced Trunk Check Features

| Options & Flags      | Explanation                                                                                                                                                              |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `--root`             | Explicitly set the root of the repository to run against                                                                                                                 |
| `--upstream`         | Specify the upstream branch used to calculate new vs existing issued.                                                                                                    |
| `--trigger`          | Supports running trunk check from inside a git hook. Options are manual (default), git-push, git-commit. Controls whether the check returns early and its interactivity. |
| `--output=format`    | Output results in specified format: `text` (default) or `json`                                                                                                           |
| `--output-file=FILE` | Write json results to specified file                                                                                                                                     |

## Details

#### --filter

`--filter` argument allows you to restrict `trunk check` to a subset of the linters enabled in your repository.

For example, to run `eslint` and `isort` on the entire repo:

```bash
trunk check --all --filter=eslint,isort
```

Alternatively, to run every linter _except_ `clang-tidy` and `shellcheck`:

```bash
trunk check --all --filter=-clang-tidy,-shellcheck
```

#### --sample

`--sample=N` will attempt to run every enabled linter against the requested number of files. The goal of the `sample` flag is to test the setup of the linters in your repository as well as any specific configuration they might honor.

The sample command will attempt to run each linter N times, but may run fewer if not enough applicable files exist in your set of files to lint. `--sample=N` can be combined with any other set of options for `trunk check`.

For example, to run `prettier` against 10 different prettier supported files:

```bash
trunk check --sample=10 --filter=prettier
```

Alternatively, to run every linter at most 5 times against its supported files:

```bash
trunk check --sample=5
```
