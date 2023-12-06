# Check

## Check란 무엇인가요?

트렁크 체크는 리포지토리, 로컬 및 CI에서 [100개 이상의 코드 검사 도구](https://github.com/trunk-io/plugins)를 실행하여 자동으로 다음과 같은 작업을 수행할 수 있습니다.

- 코드베이스에서 버그 및 보안 취약점 제거
- 코드 서식 및 스타일 적용
- 이미지 최적화
- 비밀 플래그 지정
- 그리고 훨씬 더 많은 기능들

Check는 일반적으로 로컬 도구([CLI](./CLI.md), [VS 코드 확장](https://marketplace.visualstudio.com/items?itemName=trunk.io)) 및 [CI 도구](./Non-GitHub%20Integration.md) / [GitHub 봇](./GitHub%20Integration.md) 등으로 사용됩니다.

## 눈에 띄는 기능

- 서식 지정, 린팅, 보안 검사, 이미지 최적화에 이르기까지 모든 작업을 수행합니다.
- config-as-code (`.trunk/trunk.yaml`)
- 캐싱
- 기존 문제 감지
- 데몬 및 랭귀지 서버
- [githooks 관리](./Git%20Hooks.md)
- CLI 사용 가능 ([docs](./CLI.md))
- 리포지토리 통계 및 슬랙 알림을 위한 [웹 앱](https://app.trunk.io/)

## 저는 대규모 조직을 운영하고 있습니다. 왜 Check를 사용해야 하나요?

파일을 수정할 때 trunk check에는 _새로운_ 이슈에만 플래그를 지정하는 Hold The Line이라는 기능이 있습니다. 이 기능은 깃을 인식하고 몇 가지 정교한 휴리스틱을 사용하여 코드 한 줄을 변경했는데 그로 인해 다른 곳에서 새로운 이슈가 발생하는 경우를 처리합니다.

이 기능은 개발자가 모든 파일의 기존 이슈를 일일이 수정하지 않고도 코드베이스의 레벨을 높일 수 있어 개발 속도를 크게 늦추고 많은 불만을 야기할 수 있기 때문에 엔지니어링 조직에 매우 중요합니다. 이 기능이 없으면 기존 코드 검사 도구에서 새로운 규칙을 설정하는 것도 비현실적이지만, 코드 검사 도구에 새로운 도구를 추가하는 것도 비현실적입니다. 이 기능을 사용하면 `trunk check enable {linter}`처럼 간단하게 새 도구를 추가할 수 있으며, 앞으로 더 나은 사람이 되어 기술 부채를 점진적으로 해결해 나갈 수 있습니다.

## 지원되는 린터, 포맷터 및 보안 도구

저희는 매 릴리스마다 새로운 린터를 통합합니다. [Slack](https://slack.trunk.io)에 들러 다음에 원하는 기능을 알려주세요! 모든 도구 통합은 [여기](https://github.com/trunk-io/plugins)에서 오픈 소스로 제공됩니다.

다음 도구를 활성화합니다:

```sh
trunk check enable {linter}
```

| Technology      | Linters                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| --------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| All             | [codespell](https://github.com/codespell-project/codespell#readme), [cspell](https://github.com/streetsidesoftware/cspell#readme), [gitleaks](https://github.com/zricethezav/gitleaks#readme), [git-diff-check](https://git-scm.com/docs/git-diff)                                                                                                                                                                                                                                                                                                  |
| Ansible         | [ansible-lint](https://github.com/ansible/ansible-lint#readme)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| Bash            | [shellcheck](https://github.com/koalaman/shellcheck#readme), [shfmt](https://github.com/mvdan/sh#readme)                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| Bazel, Starlark | [buildifier](https://github.com/bazelbuild/buildtools/blob/master/buildifier/README.md)                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| C, C++          | [clang-format](https://clang.llvm.org/docs/ClangFormat.html), [clang-tidy](https://clang.llvm.org/extra/clang-tidy/), [include-what-you-use](https://github.com/include-what-you-use/include-what-you-use#readme), [pragma-once](./Supported%20Linters.md/pragma-once/)                                                                                                                                                                                                                                                                             |
| CircleCI Config | [circleci](https://github.com/CircleCI-Public/circleci-cli#readme)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| Cloudformation  | [cfnlint](https://github.com/aws-cloudformation/cfn-lint#readme), [checkov](https://github.com/bridgecrewio/checkov#readme)                                                                                                                                                                                                                                                                                                                                                                                                                         |
| CSS, SCSS       | [stylelint](https://github.com/stylelint/stylelint#readme)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| Cue             | [cue-fmt](https://cuelang.org/)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| Docker          | [hadolint](https://github.com/hadolint/hadolint#readme), [checkov](https://github.com/bridgecrewio/checkov#readme)                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| Dotenv          | [dotenv-linter](https://github.com/dotenv-linter/dotenv-linter#readme)                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| GitHub          | [actionlint](https://github.com/rhysd/actionlint#readme)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| Go              | [gofmt](https://pkg.go.dev/cmd/gofmt), [gofumpt](https://pkg.go.dev/mvdan.cc/gofumpt), [goimports](https://pkg.go.dev/golang.org/x/tools/cmd/goimports), [gokart](https://github.com/praetorian-inc/gokart), [golangci-lint](https://github.com/golangci/golangci-lint#readme), [golines](https://pkg.go.dev/github.com/segmentio/golines), [semgrep](https://github.com/returntocorp/semgrep#readme)                                                                                                                                               |
| HAML            | [haml-lint](https://github.com/sds/haml-lint#readme)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| HTML Templates  | [djlint](https://github.com/Riverside-Healthcare/djlint#readme)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| Java            | [google-java-format](https://github.com/google/google-java-format#readme), [semgrep](https://github.com/returntocorp/semgrep#readme)                                                                                                                                                                                                                                                                                                                                                                                                                |
| Javascript      | [eslint](https://github.com/eslint/eslint#readme), [prettier](https://github.com/prettier/prettier#readme), [rome](https://github.com/rome/tools#readme), [semgrep](https://github.com/returntocorp/semgrep#readme)                                                                                                                                                                                                                                                                                                                                 |
| JSON            | [eslint](https://github.com/eslint/eslint#readme), [prettier](https://github.com/prettier/prettier#readme), [semgrep](https://github.com/returntocorp/semgrep#readme)                                                                                                                                                                                                                                                                                                                                                                               |
| Kotlin          | [detekt](https://github.com/detekt/detekt#readme)[1](./#note-detekt), [ktlint](https://github.com/pinterest/ktlint#readme)                                                                                                                                                                                                                                                                                                                                                                                                                          |
| Kubernetes      | [kube-linter](https://github.com/stackrox/kube-linter#readme)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| Lua             | [stylua](https://github.com/JohnnyMorganz/StyLua/tree/main)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| Markdown        | [markdownlint](https://github.com/DavidAnson/markdownlint#readme), [remark-lint](https://github.com/remarkjs/remark-lint#readme)                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Nix             | [nixpkgs-fmt](https://github.com/nix-community/nixpkgs-fmt)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| package.json    | [sort-package-json](https://github.com/keithamus/sort-package-json#readme)                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| PNG             | [oxipng](https://github.com/shssoichiro/oxipng#readme)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| Protobuf        | [buf](https://github.com/bufbuild/buf#readme) (breaking, lint, and format), [clang-format](https://clang.llvm.org/docs/ClangFormat.html), [clang-tidy](https://clang.llvm.org/extra/clang-tidy/)                                                                                                                                                                                                                                                                                                                                                    |
| Python          | [autopep8](https://github.com/hhatto/autopep8#readme), [bandit](https://github.com/PyCQA/bandit#readme), [black](https://github.com/psf/black#readme), [flake8](https://github.com/PyCQA/flake8#readme), [isort](https://github.com/PyCQA/isort#readme), [mypy](https://github.com/python/mypy#readme), [pylint](https://github.com/PyCQA/pylint#readme), [semgrep](https://github.com/returntocorp/semgrep#readme), [yapf](https://github.com/google/yapf#readme), [ruff](https://github.com/charliermarsh/ruff), [sourcery](https://sourcery.ai/) |
| Renovate        | [renovate](https://github.com/renovatebot/renovate#readme)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| Ruby            | [brakeman](https://github.com/presidentbeef/brakeman#readme), [rubocop](https://github.com/rubocop/rubocop#readme), [rufo](https://github.com/ruby-formatter/rufo#readme), [semgrep](https://github.com/returntocorp/semgrep#readme), [standardrb](https://github.com/testdouble/standard#readme)                                                                                                                                                                                                                                                   |
| Rust            | [clippy](https://github.com/rust-lang/rust-clippy#readme), [rustfmt](https://github.com/rust-lang/rustfmt#readme)                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| Scala           | [scalafmt](https://github.com/scalameta/scalafmt#readme)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| Security        | [nancy](https://github.com/sonatype-nexus-community/nancy#readme), [trivy](https://github.com/aquasecurity/trivy#readme), [tfsec](https://github.com/aquasecurity/tfsec), [osv-scanner](https://github.com/google/osv-scanner), [trufflehog](https://github.com/trufflesecurity/trufflehog/)                                                                                                                                                                                                                                                        |
| SQL             | [sqlfluff](https://github.com/sqlfluff/sqlfluff#readme), [sqlfmt](https://github.com/tconbeer/sqlfmt#readme), [sql-formatter](https://github.com/sql-formatter-org/sql-formatter#readme)                                                                                                                                                                                                                                                                                                                                                            |
| SVG             | [svgo](https://github.com/svg/svgo#readme)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| Swift           | [stringslint](https://github.com/dral3x/StringsLint#readme), [swiftlint](https://github.com/realm/SwiftLint#readme), [swiftformat](https://github.com/nicklockwood/SwiftFormat#readme)                                                                                                                                                                                                                                                                                                                                                              |
| Terraform       | [terraform](https://developer.hashicorp.com/terraform/cli/code) (validate and fmt), [checkov](https://github.com/bridgecrewio/checkov#readme), [tflint](https://github.com/terraform-linters/tflint#readme)[2](./#note-tflint), [tfsec](https://github.com/aquasecurity/tfsec)                                                                                                                                                                                                                                                                      |
| TOML            | [taplo](https://github.com/tamasfe/taplo#readme)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Typescript      | [eslint](https://github.com/eslint/eslint#readme), [prettier](https://github.com/prettier/prettier#readme), [rome](https://github.com/rome/tools#readme), [semgrep](https://github.com/returntocorp/semgrep#readme)                                                                                                                                                                                                                                                                                                                                 |
| YAML            | [prettier](https://github.com/prettier/prettier#readme), [semgrep](https://github.com/returntocorp/semgrep#readme), [yamllint](https://github.com/adrienverge/yamllint#readme)                                                                                                                                                                                                                                                                                                                                                                      |

1. Detekt에 대한 지원은 현재 개발 중이며, 자세한 내용은 [Detekt 문서](https://docs.trunk.io/docs/check-supported-linters#detekt)를 참조하세요.
2. [모듈 검사](https://github.com/terraform-linters/tflint/blob/master/docs/user-guide/module-inspection.md), [딥 체킹](https://github.com/terraform-linters/tflint-ruleset-aws/blob/master/docs/deep_checking.md), 변수 설정은 현재 지원되지 않습니다.

## 작동 방식

트렁크는 온디맨드 실행에 필요한 모든 것을 다운로드하여 `~/.cache/trunk`에 캐시합니다. 린터를 백그라운드에서 병렬로 실행하고 [언어 서버](https://microsoft.github.io/language-server-protocol)로 기능하여 \[Trunk VSCode Extension]\[vscode]를 통해 VSCode에 결과를 인라인으로 표시합니다.

## CLI 설치

`trunk` 명령줄 도구는 코드가 있는 개발자 컴퓨터에서 로컬로 실행됩니다. 나중에 트렁크 체크 클라우드를 사용하여 리포지토리를 자동으로 스캔할 수 있습니다.

Check는 자동으로 도구를 최신 상태로 유지하고, 프로젝트 유형에 맞는 표준 도구(예: C++의 경우 `clang-format`, JS/TS의 경우 `eslint`)를 제안하며, 트렁크 체크의 재현성을 보장하기 위해 `trunk.yaml`에 버전을 핀으로 지정합니다.

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

## `trunk` 초기화

git 리포지토리의 루트에서 실행합니다:

```sh
trunk init
```

이렇게 하면 리포지토리를 스캔하여 [Trunk Check](./Check.md)가 권장하는 모든 린터, 포맷터 및 보안 분석기를 사용할 수 있는 `.trunk/trunk.yaml`을 생성합니다. 자세한 내용은 [여기](./Init%20in%20a%20git%20repo.md)를 참조하세요.

## VSCode 확장 프로그램 설치

VSCode를 실행하는 경우 [트렁크 VSCode 확장](vscode:extension/Trunk.io)을 설치할 수도 있습니다. 자세한 내용은 [여기](https://marketplace.visualstudio.com/items?itemName=Trunk.io)를 참조하세요.

## `trunk check` 실행

`trunk` 는 git을 인식하므로 추가 인수가 없으면 리포지토리에서 변경한 파일에 대해 실행됩니다:

```sh
trunk check
```

참고: 파일을 수정하지 않은 경우 `trunk check`는 아무것도 검사하지 않습니다. 기본적인 `trunk` 사용법에 대한 자세한 내용은 [CLI 사용법 문서](./Usage.md)를 참조하세요.
