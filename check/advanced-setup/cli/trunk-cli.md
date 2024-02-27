# Trunk CLI

Learn more about the Trunk CLI.

### Getting Started

To use `trunk` locally, install via:

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

### What can it do?

The Trunk CLI can be used for:

- [Trunk Check](https://docs.trunk.io/check): a pluggable superlinter with a builtin language server and pre-existing issue detection
- [Trunk Tools:](https://docs.trunk.io/check/advanced-setup/tools) hermetic runtime and CLI tool manager
- [Trunk Actions](https://docs.trunk.io/check/advanced-setup/actions): local workflow automation and githooks manager
- [Trunk Merge](https://docs.trunk.io/merge): a merge queue to make merging code in github safer and easier

[Trunk Check](https://docs.trunk.io/check) and [Trunk Actions](https://docs.trunk.io/check/advanced-setup/actions) can be used entirely locally without depending on hosted services or even having a Trunk account

### Initialize Trunk in your repo

Whether you aim to use [Trunk Check](https://docs.trunk.io/check), [Trunk Merge](https://docs.trunk.io/merge), [Trunk Actions](https://docs.trunk.io/check/advanced-setup/actions), or all of the above, the first step is to initialize Trunk in your git repo:

```shell
trunk init
```

Note: for an extra layer of security you can optionally run `trunk init --lock` instead of `trunk init` which adds sha256s of the trunk cli to the trunk config file.

This is then used by the Trunk Launcher when it downloads the `trunk` binary.

`init` scans the files in your repo and generates a `.trunk/trunk.yaml` configuration file tailored to your repo (it may also generate linter-specific config files, such as `.shellcheckrc`).

The scan will identify all the particular languages and technologies you use and automatically configure the correct set of linters / formatters to run.

If you only want to use [Trunk Merge](https://docs.trunk.io/merge), you can safely ignore the linter setup, or even strip enabled linters from `.trunk/trunk.yaml`.

You will additionally need to login to use [Trunk Merge](https://docs.trunk.io/merge) via `trunk login`.
