# Getting Started

How to install the Trunk CLI to check your code in less than 60 seconds.

The `trunk` command line tool runs locally on your developer machine, right where your code is.

Later, you can automatically scan your repo using the [Trunk Check webapp](https://docs.trunk.io/check/check-cloud-ci-integration).

Trunk Check will automatically keep tools up to date, suggest standard tools for your project type (eg: `clang-format` for C++, `eslint` for JS/TS), and pin versions in `trunk.yaml` to ensure Trunk Check is reproducible.

## Install the CLI

Run one of the following to install the `trunk` command line tool.

```shell
curl https://get.trunk.io -fsSL | bash
```

```shell
curl https://get.trunk.io -fsSL | bash -s -- -y
```

```shell
brew install trunk-io
```

```shell
npm install -D @trunkio/launcher
```

```shell
pnpm add -D @trunkio/launcher
```

```shell
yarn add -D @trunkio/launcher
```

## Initialize `trunk`

From the root of a git repo, run:

```shell
trunk init
```

This will scan your repository and create a `.trunk/trunk.yaml` that enables all the linters, formatters, and security analyzers that [Trunk Check](https://docs.trunk.io/check) recommends.

For more details, see [here](https://docs.trunk.io/check/advanced-setup/cli/init-in-a-git-repo).

## Basic Usage

The main commands when running `trunk` from the command line are:

```shell
trunk check       # runs the universal linter on all applicable files
trunk fmt         # runs all the enabled formatters and auto-applies changes
```

You can always find this list using `trunk check --help`.

Trunk is git-aware.

When you run `trunk check` it will **only run on files you've modified according to git**.

To run on a sampling in your repo, run: `trunk check --sample 5`

### check

`trunk check` runs linters & formatters on your changed files, prompting you to apply fixes.

Without additional args, `trunk check` will run all applicable linters on all files changed in the current branch.

### fmt

Run all applicable formatters as configured in `trunk.yaml`. `trunk fmt` is short-hand for running `trunk check` with a `--fix --filter` set to all formatters enabled in your repository.

## Options

| options      |                                                                                                                                       |
| :----------- | :------------------------------------------------------------------------------------------------------------------------------------ |
| `--all`      | Run on all the files in the repository. Useful if trying to assess a new linter in the system, or to find and fix pre-existing issues |
| `--fix`      | Auto-apply all suggested fixes                                                                                                        |
| `--no-fix`   | Surface, but do not prompt for autofixes                                                                                              |
| `--filter`   | List of comma-separated linters to run. Specify `--filter=-linter` to disable a linter.                                               |
| `--sample=N` | Run check on a [sampling](https://docs.trunk.io/check/advanced-setup/cli/cli-options#sample) of all files in the repo                 |
| `--help`     | Output help information                                                                                                               |

### Recipes

| Check                                                        | Command                                      |
| ------------------------------------------------------------ | -------------------------------------------- |
| all files                                                    | `trunk check --all --no-fix`                 |
| a specific file                                              | `trunk check some/file.py`                   |
| all applicable files with flake8                             | `trunk check --all --no-fix --filter=flake8` |
| a selection of five files in the repo                        | `trunk check --sample 5`                     |
| a selection of five files in the repo with a specific linter | `trunk check --sample 5 --filter=flake8`     |
| format the whole repo                                        | `trunk fmt --all`                            |
| format a specific file                                       | `trunk fmt some/file.py`                     |
| format all python code with `black`                          | `trunk fmt --all --filter=black`             |

### Manage the linters

List all of the available linters

```shell
trunk check list
```

Enable a single linter

```shell
trunk check enable <linter name>
```

Disable a single linter

```shell
trunk check disable <linter name>
```

## CI Setup

Once you have Trunk Check configured on your local machine, you can set up the Trunk Check webapp to automatically run checks whenever your CI system builds and runs tests.

See [Continuous Integration Setup](https://docs.trunk.io/check/check-cloud-ci-integration) for more information.

## Hold the Line

By default Trunk Check will _Hold The Line_, meaning it will only run against new changes in your codebase, not old ones.

For more see [Hold the Line](https://docs.trunk.io/check/configuration/hold-the-line).

## Ignoring Issues

To tell Trunk Check to ignore a line in your source code with a special comment like this:

```cpp
struct FooBar {
  // trunk-ignore(clang-tidy)
  void *ptr = NULL;
};
```

The comment should contain the name of the linter you want to ignore the following line, in this case `clang-tidy` For more complex ignore commands, see [Ignoring Issues](https://docs.trunk.io/check/configuration/ignoring-issues).

## Upgrading

`trunk upgrade` will upgrade the Trunk CLI, along with all plugins and linters in your `trunk.yaml`.

We highly recommend running on the latest validated versions of tools as updates will frequently include important security fixes and additional valuable checks.

Trunk only auto-suggests linter upgrades to versions that we have tested and support, so you may see a slight lag time when a new linter version is released.

```shell
trunk upgrade
```

`trunk upgrade` will also recommend new tools that have become applicable since the last time your repository was scanned.

This can be a result of using new technologies in your repository or trunk itself adding support for more tools.

If you don't like a particular recommendation you can run `trunk check disable <linter>` to teach trunk not to recommend it.
