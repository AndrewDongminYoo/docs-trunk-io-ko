---
description: Using Trunk Check on Windows
---

# Windows Support (beta)

> 📘 Trunk Check is available for beta on Windows!
>
> Try it out by following the instructions below.

### Getting Started

From **`git-bash` or `msys2`**, download the Bash launcher and add it to your `PATH`:

```bash
curl https://get.trunk.io -fsSL | bash
```

From **`powershell`**, download the powershell launcher:

```Text
Invoke-RestMethod -Uri https://trunk.io/releases/trunk.ps1 -OutFile trunk.ps1
```

Ensure you can execute powershell scripts:

```Text
Set-ExecutionPolicy Bypass -Scope CurrentUser
```

You can then execute trunk as `.\trunk.ps1`.

### Compatibility

Trunk only supports Windows with the following versions and above:

| Tool    | Where to Modify                                     | Minimum Required Version |
| :------ | :-------------------------------------------------- | :----------------------- |
| CLI     | `cli` `version` in `.trunk/trunk.yaml`              | `1.13.0`                 |
| Plugins | `ref` for the `trunk` plugin in `.trunk/trunk.yaml` | `v1.0.0`                 |
| VSCode  | Reload VSCode to update                             | `3.4.4`                  |

You will also need to install [C and C++ runtime libraries](https://aka.ms/vs/17/release/vc_redist.x64.exe) in order to run some linters.

### Getting Help

Thank you for being a beta tester of Trunk Check on Windows! We are actively working to improve the experience. If you have any feedback or questions, please reach out to us directly on [Slack](https://slack.trunk.io/).

If you want to override a repo-wide setting just for your Windows machine, you can modify your [`.trunk/user.yaml`](../../reference/user-yaml.md).

### Supported Features

We intend to bring full feature support to Windows for Trunk. Currently, the following features are supported:

- [Trunk Check](../../)
- Non-interactive [Trunk Actions](../actions/) and [git-hooks](../actions/git-hooks.md)
- [VSCode](../../ide-integration/vs-code.md)

### Unsupported Linters (as of Plugins v1.2.0)

Trunk runs most linters on all platforms. However, some linters are not yet supported on Windows. For a full list of all linters, see our [Plugins repo](https://github.com/trunk-io/plugins).

| Linter               | Plans for Support                       |
| :------------------- | :-------------------------------------- |
| ansible-lint         | Only supported on WSL                   |
| clang-format         | Long-term plans for LLVM linter support |
| clang-tidy           | Long-term plans for LLVM linter support |
| detekt-gradle        | Long-term plans for support             |
| include-what-you-use | Long-term plans for LLVM linter support |
| nixpkgs-fmt          | Long-term plans for support             |
| perlcritic           | No immediate plans for support          |
| perltidy             | No immediate plans for support          |
| scalafmt             | No download available for Windows       |
| semgrep              | No download available for Windows       |
| shellcheck           | No download available for Windows       |
| stringslint          | Only supported on MacOS                 |
| swiftformat          | Only supported on MacOS                 |
| swiftlint            | Only supported on MacOS                 |
| taplo                | No download available for Windows       |
