# Install Trunk

The below commands install the Trunk Launcher, a bash script that downloads the appropriate Trunk CLI version and runs it. The launcher invisibly runs the Trunk CLI version specified in a project's `.trunk/trunk.yaml` file. The actual Trunk CLI is a single binary that is cached locally in `~/.cache/trunk` and is updated automatically.

Run one of the following commands to install the Trunk Launcher, or add it as a dev dependency to your project if you use `npm`, `pnpm`, or `yarn`. You can also commit the Trunk launcher directly into your repo (see below).

```sh
curl https://get.trunk.io -fsSL | bash

# 프롬프트 응답 없이 설치하려면:
curl https://get.trunk.io -fsSL | bash -s -- -y

# OSX Homebrew로 설치하기
brew install trunk-io

# NPM으로 설치하기
npm install -D @trunkio/launcher

# PNPM으로 설치하기
pnpm add -D @trunkio/launcher

# Yarn으로 설치하기
yarn add -D @trunkio/launcher
```

For use on Windows, check out our [Windows (beta)](<./Windows%20Support%20(beta).md>) page.

## Commit the Trunk Launcher (optional)

To allow your teammates to use `trunk` without installing anything, the launcher can be committed directly into your repo:

```sh
curl -LO https://trunk.io/releases/trunk
chmod +x ./trunk
git commit ./trunk -m "Commit Trunk to our repo"
```

This makes it much easier for you to share Trunk with your colleagues!

## Uninstall instructions

### From your system

Trunk has a very minimal installation, and therefore, there's not much to uninstall. The two system paths we use are:

- `/usr/local/bin/trunk`: the [Trunk Launcher](./Components.md#trunk-launcher)
- `~/.cache/trunk`: cached versions of the trunk cli, linters, formatters, etc.

You can delete those two paths to uninstall.

### From a repo

To cleanly remove trunk from a particular repo, run:

```sh
trunk deinit
```

### VS Code extension

To uninstall the Trunk VS Code extension, do so as you would any extension ([docs](https://code.visualstudio.com/docs/editor/extension-marketplace)). Then reload VS Code.

## Binary download (not recommended)

You can directly download the trunk binary. \*\*We don't recommend this mode of operation because your ability to version the tool through `trunk.yaml` will not function when launching `trunk` directly from a downloaded binary. Regardless you can bypass the launcher support by downloading the prebuilt binaries here:

| variable | options                                       |
| :------- | :-------------------------------------------- |
| version  | the semver of the binary you want to download |
| platform | 'darwin', 'linux'                             |

```sh
# for example https://trunk.io/releases/1.0.0/trunk-1.0.0-linux-x86_64.tar.gz
https://trunk.io/releases/${version}/trunk-${version}-${platform}-x86_64.tar.gz
```
