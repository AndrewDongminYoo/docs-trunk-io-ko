# Configuration

`trunk check` 및 `trunk fmt`에 대한 구성은 git 리포지토리의 `.trunk` 디렉터리에 저장된 [`trunk.yaml`](./Trunk%20YAML.md#cli)파일의 `lint` 섹션에서 관리합니다. 다음은 모든 기능을 갖춘 `lint` 섹션의 모습에 대한 예시입니다:

```yaml
lint:
  # list of linters to run using trunk
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
  threshold:
    - linters: [clang-tidy]
      level: high
  triggers:
    - linters:
        - ansible-lint
      paths:
        - ansible # A directory
      targets:
        - ansible # A directory
```

## Test your config changes

일반적으로 `trunk check` 또는 `trunk fmt`를 실행하면 변경한 파일에 대해서만 실행됩니다. 하지만 이제 저장소에서 린터가 어떻게 작동하는지 쉽게 테스트할 수 있습니다. 린터 구성을 조정할 때 `--샘플` 플래그와 함께 check를 실행하기만 하면 됩니다. 샘플링을 실행하면 Trunk가 활성화한 린터에 해당하는 파일을 찾아서 입력 샘플링에 대해 린터가 어떻게 작동하는지 보여줍니다.

| 샘플                        | 명령어                                   |
| :-------------------------- | :--------------------------------------- |
| 5개 파일에 대한 모든 린터   | `trunk check --sample 5`                 |
| 7개의 파일이 있는 특정 린터 | `trunk check --sample=7 --filter=clippy` |
| 특정 파일에 있는 모든 린터  | `trunk check foo.file`                   |

샘플링할 때 `trunk`는 단일 파일에 대해 여러 개의 린터를 실행할 수 있으며, 요청된 전체 샘플 수에 대해 각 린터를 실행하지 않을 수 있습니다(해당 파일을 충분히 찾을 수 없는 경우).

## Tuning your linters

대부분의 린터는 어떤 형태로든 구성을 제공합니다. Trunk는 실행하는 모든 도구의 기본 구성 설정을 사용합니다. 새로운 설정 언어를 배우거나 Trunk 내에서 린터를 튜닝하는 특정 방법을 찾을 필요가 없습니다. `clang-tidy`, `eslint` 또는 다른 어떤 린터든 온라인에서 찾을 수 있는 모든 문서가 여전히 적용됩니다. 저희는 거인들의 어깨 위에 서게 된 것을 자랑스럽게 생각하며, 이러한 도구를 만드는 오픈 소스 커뮤니티가 어떻게 작동해야 하는지 가장 잘 알고 있다고 믿습니다. 저희의 목표는 사용자가 이러한 도구를 최대한 쉽게 도입할 수 있도록 하는 것입니다.

린터의 기능을 구성하려면 린터 자체의 설정 파일을 계속 사용해야 합니다. 항상 최신 상태로 유지되는 린터 구성 파일과 린터 및 리포지토리를 구성하는 방법에 대한 몇 가지 팁은 [configs](https://github.com/trunk-io/configs) 리포지토리를 참조하세요. [지원되는 린터](./Supported%20Linters.md) 목록에서 린터 관련 팁을 확인하세요. Trunk에서 **어떻게** 린터를 실행할지 구성하려면 계속 읽어보세요.

### Moving linter configs

원하는 경우, Trunk는 리포지토리의 루트에서 `.trunk/configs` 폴더로 모든 린터 구성을 마이그레이션하는 기능도 지원합니다. 이러한 구성 파일은 `trunk check` 실행 중에 심볼릭 링크됩니다. 이러한 설정이 루트에 있어야 하는 LSP와 함께 clangd와 같은 IDE 확장 프로그램을 사용하는 경우 숨겨진 설정에서 워크스페이스 루트에 대한 추가 심볼릭 링크를 만들어야 합니다.

Trunk에서 특정 린터를 실행하는 방법을 조정하려면 [기본값 오버라이딩](./Trunk%20YAML.md#overriding-defaults) 및 [다양한 린터 설정](./Custom%20Linters.md) 문서를 참조하세요. 예를 들어 대부분의 린터에서 기본적으로 라인 홀드는 활성화되어 있지만 이렇게 비활성화할 수 있습니다:

```yaml
lint:
  definitions:
    - name: pylint
      hold_the_line: false
      disable_upstream: true
```

린터에 대해 줄 대기 기능을 비활성화하면 해당 파일에 대한 변경 사항을 적용하기 전에 해당 린터에서 발견된 모든 문제를 해결해야 합니다.

린터에 대한 업스트림을 비활성화하면 업스트림에서 린터가 실행되지 않습니다.

## Enable Linters

Trunk는 `enabled` 섹션에 나열된 린터만 실행하며, `linters`에 정의되어 있지만 `enabled`에 나열되지 않은 린터는 실행되지 않습니다.

린터를 활성화할 때는 린터의 버전을 지정해야 합니다:

```yaml
lint:
  enabled:
    # enabling a version with a linter
    - gitleaks@7.6.1
    - gofmt@1.16.7
    - golangci-lint@1.41.1
    - hadolint@2.6.0
```

사용자 지정 린터는 약간 다르므로 자세한 내용은 [해당 문서](./Custom%20Linters.md)를 참조하세요.

또한 `trunk 업그레이드 확인`을 실행하여 Trunk가 새로운 린터를 감지하고 기존 린터를 최신 버전으로 업그레이드하도록 요청할 수도 있습니다.

## Disable Linters

Trunk는 지속적으로 리포지토리를 모니터링하고 코드베이스에서 실행할 새로운 도구를 추가로 추천합니다. 특정 린터를 비활성화 목록에 추가하여 Trunk가 특정 린터를 추천하지 않도록 할 수 있습니다.

```yaml
lint:
  disabled:
    # disabled a linter tells trunk not to recommend it during upgrade scans
    - rufo
    - tflint
```

## Installing additional packages

린터와 함께 추가 패키지를 설치하는 것을 지원합니다. 예를 들어, Pylint는 pip 패키지로 설치할 수 있는 플러그인 추가를 지원합니다. 예를 들어, 설정의 일부로 `pylint-django` 플러그인을 실행하려면 Trunk에 패키지를 설치하도록 지시해야 합니다:

```yaml
lint:
  enabled:
    - pylint@2.11.0:
        packages:
          - pylint-django@2.4.4
        ...
```

### Runtime versioning

기본적으로 Trunk는 사용자가 선택한 린터에 필요한 런타임 버전을 설치합니다. 특정 런타임 버전에 페깅해야 하거나 시스템에 설치된 버전을 사용하려면 [런타임 문서](./Trunk%20YAML.md#runtimes)를 참조하세요.

## Ignoring files

생성된 코드와 같은 일부 파일은 검사할 필요가 없습니다. 이러한 파일을 무시하려면 `ignore` 키를 사용하세요:

```yaml
lint:
  ignore:
    - linters: [ALL]
      paths:
        # Ignore generated files
        - src/generated/**
        # Except for files ending in .foo
        - !src/generated/**/*.foo # Test data


        - test/test_data
```

`ignore`의 모든 항목은 무시할 린터 집합과 경로 집합을 모두 정의합니다.

| 키        | 값                                                                                                                                                                                                                |
| :-------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `linters` | 린터 목록(예: `[black, eslint]`) 또는 특수 `[ALL]` 태그                                                                                                                                                           |
| 경로`     | 무시할 리포지토리의 루트를 기준으로 한 [글로브 경로](./Glob%20Path%20Pattern.md) 목록. 경로가 `!`로 시작하면 역무시를 나타냅니다. 즉, 해당 글로브와 일치하는 파일은 다른 글로브와 일치하더라도 무시되지 않습니다. |

`trunk`는 `git`을 인식하므로 기본적으로 `gitignore`d` 파일을 무시합니다.

## Blocking Thresholds

모든 [이슈 심각도](./Ignoring%20Issues.md)는 기본적으로 차단으로 간주됩니다. 새로운 린터를 천천히 시험해보고 싶은 경우, 각 린터에 대해 특정 임계값을 설정하는 메커니즘을 제공합니다.

```yaml
lint:
  threshold:
    - linters: [clang-tidy]
      level: high
```

`threshold`의 모든 항목은 차단으로 간주되는 린트 세트와 심각도 임계값을 정의합니다. 이 예제에서는 `clang-tidy`에 대해 `high` 린트 문제만 차단으로 간주해야 한다고 말합니다.

| 키      | 값                                                                                                                                         |
| :------ | :----------------------------------------------------------------------------------------------------------------------------------------- | --- |
| linters | 린터 목록(예: `[black, eslint]`) 또는 특수 `[ALL]` 태그                                                                                    |
| level   | 이슈가 차단된 것으로 간주되는 임계값. 다음 중 하나입니다: `low`, `medium`, `high` 또는 `none`(마지막 옵션을 사용하면 이슈가 차단되지 않음) |     |

## Trigger rules

일부 린터는 개별 파일에 대해 작동하지 않습니다. 대신 전체 리포지토리를 한 번에 린팅해야 합니다. Trunk에서 이를 처리하는 방법은 트리거 규칙을 설정하는 것입니다.

트리거 규칙은 3가지 원칙에 따라 작동합니다:

1. 린팅을 트리거하는 입력. 파일, 디렉터리 또는 확장 글로브가 될 수 있습니다.
2. 트리거된 파일이 수정될 때 실행할 린터(들).
3. 린터에 전달할 타깃(파일 또는 디렉터리일 수 있음).

ansible-lint의 예시:

```yaml
lint:
  enabled:
    - ansible-lint@5.3.2

  triggers:
    - linters:
        - ansible-lint
      paths:
        - ansible # A directory
      targets:
        - ansible # A directory
```

트리거된 린터는 나열된 경로 중 하나와 일치하는 파일이 존재하는 한 --에브리씽으로 trunk check를 실행할 때도 실행됩니다.

"."를 대상으로 사용하여 격리된 디렉터리 대신 전체 리포지토리에 대해 실행할 수 있습니다.

## File Size

기본적으로 Trunk는 최대 4MB 크기의 파일만 정렬합니다. 이를 전역적으로 재정의하려면 `lint`에 `default_max_file_size`를 지정하세요:

```yaml
lint:
  default_max_file_size: 1048576 # Bytes
```

특정 린터에 대해 이 값을 재정의하려면 해당 정의에 `최대 파일 크기`를 지정하세요:

```yaml
lint:
  definitions:
    - name: prettier
      max_file_size: 2097152 # Bytes
```
