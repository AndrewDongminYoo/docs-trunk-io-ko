### 

Getting Started[](https://docs.trunk.io/check/cli#getting-started)

To use `trunk` locally, install via:

curl https://get.trunk.io \-fsSL | bash

### 

What can it do?[](https://docs.trunk.io/check/cli#what-can-it-do)

The Trunk CLI can be used for:

-   [Trunk Check](https://github.com/trunk-io/gitbook/blob/main/check/README.md): a pluggable superlinter with a builtin language server and pre-existing issue detection
    

-   [Trunk Merge](https://github.com/trunk-io/gitbook/blob/main/merge-graph/README.md): a merge queue to make merging code in github safer and easier
    

[Trunk Check](https://github.com/trunk-io/gitbook/blob/main/check/README.md) and [Trunk Actions](https://github.com/trunk-io/gitbook/blob/main/check/actions/README.md) can be used entirely locally without depending on hosted services or even having a Trunk account

### 

Initialize Trunk in your repo[](https://docs.trunk.io/check/cli#initialize-trunk-in-your-repo)

Note: for an extra layer of security you can optionally run `trunk init --lock` instead of `trunk init` which adds sha256s of the trunk cli to the trunk config file. This is then used by the Trunk Launcher when it downloads the `trunk` binary.

`init` scans the files in your repo and generates a `.trunk/trunk.yaml` configuration file tailored to your repo (it may also generate linter-specific config files, such as `.shellcheckrc`). The scan will identify all the particular languages and technologies you use and automatically configure the correct set of linters / formatters to run.

If you only want to use [Trunk Merge](https://github.com/trunk-io/gitbook/blob/main/merge-graph/merge/README.md), you can safely ignore the linter setup, or even strip enabled linters from `.trunk/trunk.yaml`. You will additionally need to login to use [Trunk Merge](https://github.com/trunk-io/gitbook/blob/main/merge-graph/merge/README.md) via `trunk login`.

### 

Next Steps[](https://docs.trunk.io/check/cli#next-steps)