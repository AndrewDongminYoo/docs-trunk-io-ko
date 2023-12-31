# Trunk YAML (.trunk/trunk.yaml)

`trunk` is controlled by the `.trunk/trunk.yaml` file in your repository.

```log
/your_repo
├── .trunk
│   └── trunk.yaml
└── src
    ├── bar
    └── foo
```

This is initially generated by `trunk init` and is the central source of truth for how `trunk` operates inside your repository. As we build new services and features, we'll extend `trunk.yaml` to include configuration sections for them. We believe strongly in configuration as code and being able to guarantee that `trunk` can be run reproducibly.

The trunk configuration file is written in YAML and our hope is that it is self-descriptive. We've included below a sample config file in its entirety to help you understand how the pieces all come together. Alternatively, you can also refer to [the `trunk.yaml` in our GitHub Action](https://github.com/trunk-io/trunk-action/blob/main/.trunk/trunk.yaml) as an example or [`trunk-yaml-schema.json`](https://static.trunk.io/pub/trunk-yaml-schema.json).

```yaml
version: 0.1 # the version of this config file.
cli:
  version: 0.15.1 # the version of trunk you will run in your repository
runtimes:
  enabled:
    - ruby@>=2.7.1
    - python@3.9.1
lint:
  definitions:
    - name: my_custom_linter
      files: [ALL]
      commands:
        output: sarif
        run: ${workspace}/bin/foo --file ${target}
        read_output_from: stdout
        run_linter_from: workspace
        success_codes: [0, 1]
  enabled:
    - ansible-lint@5.3.2
    - bandit@1.7.0
    - black@21.6b0
    - buf-lint@1.0.0-rc3
    - buildifier@5.1.0
    - cfnlint@0.51.0
    - eslint@7.30.0
    - gitleaks@7.6.1
    - gofmt@1.16.7
    - golangci-lint@1.41.1
    - hadolint@2.6.0
    - isort@5.8.0
    - markdownlint@0.28.1
    - mypy@0.910
    - prettier@2.3.2
    - pylint@2.8.1
    - rustfmt@1.55.0
    - semgrep@0.104.0
    - shellcheck@0.7.2
    - shfmt@3.3.1
  disabled:
    - rufo
    - tflint
  ignore:
    - linters: [ALL]
      paths:
        # Generated files
        - a/proto/code_pb*
        # Test data
        - b/test_data/**
    - linters: [eslint]
      paths:
        - c/run.ts
  triggers:
    - linters:
        - ansible-lint
      paths:
        - ansible # A directory
      targets:
        - ansible # A directory
```

The first two sections of this file are self-explanatory: `version` is the schema version of `trunk.yaml` and `cli.version` is the version of the `trunk` CLI that we have pinned.

## `cli`

```yaml
cli:
  version: 0.15.1 # the version of trunk you will run in your repository
  options:
    - commands: [ALL] # apply to all `trunk` commands
      args: --monitor=true
    - commands: [check, fmt] # apply only to `trunk check` and `trunk fmt` commands
      args: -y
```

In addition to specifying `version`, `cli` allows you to specify default command line arguments using the `options` field. Specified `args` will be appended to strictly matched `commands` during `trunk` invocations. Specifying `ALL` as a `commands` element applies its options to all `trunk` subcommands. Any command line options will take precedence over these `args`.

Some examples using the configuration above:

- `trunk check` resolves to `trunk check -y --monitor=true`
- `trunk check -n` resolves to `trunk check -n --monitor=true`
- `trunk fmt` resolves to `trunk fmt -y --monitor=true`

## `repo`

```yaml
repo:
  # main is the branch that everyone's work is merged into
  # (this is usually inferred and not required to be set)
  trunk_branch: main
```

Some `trunk` features require `trunk` to be aware of the canonical repository your organization uses, i.e. the repository which everyone pulls from and makes pull requests into. `trunk` can infer this from your `origin` remote, but if you don't want your `origin` to be used for this purpose, you can explicitly specify your canonical repository.

Other features - namely `trunk check` - need to be aware of the primary upstream branch that everyone branches from. If you use `main` or `master`, `trunk` can infer this; however, if you use some other primary branch, then you may want to consider setting this.

The above configuration is how you would specify that [https://github.com/github/gitignore](https://github.com/github/gitignore) is your canonical repository and that `main` is the branch which `trunk` should always think of as your upstream branch.

## `api`

```yaml
api:
  # name of your trunk organization on app.trunk.io
  org: { your-org-name }
```

Some `trunk` features, like the CI Debugger, require knowledge of the trunk organization your repository is using. This information can be provided on the command-line or hard-coded in the `trunk.yaml` file.

### Stacked PR support

```yaml
repo:
  use_branch_upstream: true
```

By default, `trunk` will auto-detect all changed files relative to your main branch. If you would instead like it to compare against the upstream of your current git branch, you can enable this feature by setting `use_branch_upstream` to `true`.

## `runtimes`

`trunk` manages the hermetic installation of all required runtimes. You can also specifically pin a version of a runtime you'd like `trunk` to use, or tell `trunk` to re-use an already-installed runtime on the system.

By default, `trunk` will use its own hermetic runtime installations. To specify that `trunk` is allowed to use the version of a runtime available in your `PATH`, you can specify a range-based version constraint, e.g. `>=0.1.2`.

In the example config below, if there is a version of `ruby` which is at least `2.7.1` already installed on the system, `trunk` will use that system version of `ruby`; similarly, if there is a version of `python` which is exactly `3.9.1`, `trunk` will use that system version of `python`. If there is no pre-installed runtime available that satisfies the specified constraint, then `trunk` will instead install its own instance of said runtime to use.

```yaml
runtimes:
  enabled:
    - ruby@>=2.7.1 # Use system installed ruby if it matches version 2.7.1 or greater
    - python@3.9.1 # Use system python if it matches 3.9.1 exactly
```

## `lint`

See the [check configuration](./Configuration.md) and [custom linter](./Custom%20Linters.md) documentation.

### Disable upgrade notifications

Trunk will periodically tell you to upgrade to a newer version if one is available. If you prefer not to see these notifications, edit (or add) the section of your `.trunk/trunk.yaml` to include the following lines:

```yaml
actions:
  disabled:
    - trunk-upgrade-available
```

### Overriding defaults

`trunk` ships with a default configuration which `trunk.yaml` is merged into to produce the actual configuration that `trunk` runs with. You can view this merged configuration using `trunk print-config`.

You may find while using `trunk` that you want to modify one of these defaults: perhaps you want `clang-tidy` to not run on the upstream, or maybe you want the `node` runtime to include another environment variable. In these cases, you can specify the field in your `trunk.yaml` to override the default value.

Let's take `clang-tidy` as an example, which ships with the following default configuration:

```yaml
definitions:
  ...
  - name: clang-tidy
    files: [c/c++-source]
    type: llvm
    commands:
      - output: llvm
        run: clang-tidy --export-fixes=- ${target}
        success_codes: [0]
    download: clang-tidy
    direct_configs: [.clang-tidy]
    disable_upstream: true
    include_scanner_type: compile_command
    environment:
      - name: PATH
        list: ["${linter}/bin"]
  ...
```

If you wanted to flip the value of `disable_upstream` to `false`, you could, in your own `trunk.yaml`, specify:

<pre class="language-yaml"><code class="lang-yaml"><strong>definitions:
</strong>  ...
  - name: clang-tidy
    disable_upstream: false
  ...
</code></pre>

Some linters have multiple commands, such as [trivy](https://github.com/trunk-io/plugins/blob/main/linters/trivy/plugin.yaml), which can run in different ways. Similarly, some linters are configured to run differently on different platforms or at different versions. When overriding a command definition, overrides are applied on the tuple \[name, version, platforms]. For example, if you wanted to disable batching when running [ktlint](https://github.com/trunk-io/plugins/blob/main/linters/ktlint/plugin.yaml) on Windows, you could consider its default configuration:

```yaml
definitions:
  ...
  - name: ktlint
    ...
    commands:
      - name: format
        platforms: [windows]
        run: java -jar ${linter}/ktlint.exe -F "${target}"
        output: rewrite
        cache_results: true
        formatter: true
        in_place: true
        batch: true
        success_codes: [0, 1]
      - name: format
        run: ktlint -F "${target}"
        output: rewrite
        cache_results: true
        formatter: true
        in_place: true
        batch: true
        success_codes: [0, 1]
  ...
```

and override it as such:

```yaml
definitions:
  ...
  - name: ktlint
    ...
    commands:
      - name: format
        platforms: [windows]
        batch: false
  ...
```

When executing linters, Trunk will execute the first matching command based on its compatible platforms and linter version. Note when overriding that new commands that don't match an existing tuple are prepended to the resulting commands list.

Alternatively, consider the default `node` runtime:

```yaml
runtimes:
  definitions:
    - type: node
      download: node
      runtime_environment:
        - name: HOME
          value: ${home}
        - name: PATH
          list: ["${runtime}/bin"]
      linter_environment:
        - name: PATH
          list: ["${linter}/node_modules/.bin"]
      version: 16.14.2
      version_commands:
        - run: "node --version"
          parse_regex: ${semver}
```

If you wanted to add `${home}/my/special/node/path` to `PATH`, you could specify the following:

```yaml
runtimes:
  - type: node
    runtime_environment:
      - name: HOME
        value: ${home}
      - name: PATH
        list: ["${home}/my/special/node/path", "${runtime}/bin"]
```

Notes:

1. 사용자 정의 린터/다운로드/런타임 구성은 유효성 검사를 거쳐야 하며 완전히 정의되어야 하지만, 기본 제공 린터/다운로드/런타임과 이름이 일치하는 구성은 원하는 만큼 값을 적게 또는 많이 지정할 수 있습니다.
2. 스칼라 값은 간단한 방식으로 재정의됩니다. 재정의에 지정된 값이 기본값을 대신하고, 그렇지 않으면 기본값이 유지됩니다.
3. 기본값의 시퀀스 값을 재정의하려면(예: `node` 런타임의 `environment`), 새 시퀀스를 완전히 지정해야 합니다. 이것이 바로 위의 `environment` 재정의가 `HOME`도 정의하는 이유입니다. 새 값만 추가하려면 기존 시퀀스를 오버라이드 구성에 복사하고 새 값을 목록 끝에 추가해야 합니다.
4. 0이 아닌 길이의 시퀀스를 0 길이로 설정할 수 없습니다. 예를 들어 기본 구성에 `success_codes: [0]`인 경우 이를 `success_codes: [0, 1]`로 재정의할 수는 있지만 값을 지울 수는 없습니다.
5. 병합된 구성은 사용자 정의 린터와 동일한 유효성 검사가 적용되며, 모두 이름, 유형, 명령, `success_codes` 또는 `error_codes`가 설정되어 있어야 합니다.
