# Custom Linters

`trunk`의 장점은 사용자 지정 린터로 캐싱 및 대기열 유지 솔루션을 활용할 수 있다는 것인데, 린터의 결과를 어떻게 해석할지 알려주기만 하면 됩니다.

Trunk는 현재 다음과 같은 유형의 추가/전용 린터를 지원합니다:

|                                     |                |                                                                                                                                        |
| :---------------------------------- | :------------: | :------------------------------------------------------------------------------------------------------------------------------------- |
| Linter Type                         | Autofixsupport | Description                                                                                                                            |
| [`sarif`](./#sarif)                 |       ✓        | 진단을 [Static Analysis Results Interchange Format](https://docs.oasis-open.org/sarif/sarif/v2.0/sarif-v2.0.html) JSON으로 생성합니다. |
| [`lsp_json`](./#lsp-json)           |                | 진단을 [Language Server Protocol](https://microsoft.github.io/language-server-protocol/) JSON으로 생성합니다.                          |
| [`pass_fail`](./#pass-fail-linters) |                | 단일 파일 수준 진단을 `stdout`에 씁니다.                                                                                               |
| [`regex`](./#regex)                 |                | 사용자 지정 정규식 형식을 사용하여 진단을 생성합니다.                                                                                  |
| [`arcanist`](./#arcanist)           |       ✓        | 진단을 Arcanist JSON으로 생성합니다.                                                                                                   |
| [`rewrite`](./#formatters)          |       ✓        | 형식이 지정된 파일 버전을 `stdout`에 씁니다.                                                                                           |

린터가 다른 출력 유형을 생성하는 경우, [parser](./Custom%20Parsers.md)를 작성하여 린터의 출력을 Trunk가 이해할 수 있는 형식으로 변환할 수도 있습니다.

사용자 정의 린터를 설정하려면 `lint > 정의`의 `trunk.yaml`에 추가하고 활성화하세요:

```yaml
lint:
  definitions:
    - name: foo
      files: [ALL]
      commands:
        - output: sarif
          success_codes: [0, 1]
          run: ${workspace}/bin/foo --file ${target}
          read_output_from: stdout
  enabled:
    # ...
    - foo@SYSTEM
```

`@SYSTEM`은 사용자 정의 린터를 호출할 때 `PATH` 환경 변수를 사용자 정의 린터로 전달할 것을 나타내는 특수 식별자입니다.

모든 커스텀 린터는 이름, 실행할 파일 유형, 하나 이상의 명령, `success_codes` 또는 `error_codes`를 지정해야 합니다.

> 정보: `enabled`의 항목은 린터 이름과 버전을 모두 지정해야 합니다. 리포지토리에 커밋하는 경우, `@SYSTEM`을 사용하면 셸의 `PATH`를 통해 린터를 실행할 수 있습니다. 하지만 린터에 대한 버전이 지정된 릴리스 파이프라인이 있는 경우에는 [`download`](./#downloads)를 사용하여 사용자 지정 린터를 정의하고 사용할 다운로드 버전을 지정해야 합니다.

## Execution Model

`trunk check`를 실행하면 `trunk`는 다음을 수행합니다:

- 수정된 파일 집합을 계산한다 (현재 작업 트리와 `upstream-ref`, 일반적으로 `main` 또는 `master` 브랜치를 비교한다.
  보통 `main` 또는 `master` 브랜치)를 비교합니다.
- 수정된 파일을 기반으로 실행할 린트 액션 집합을 계산합니다.
  - 활성화된 각 린터는 해당 수정된 파일([details](./)) 당 한 번씩 호출됩니다.
    예를 들어, `pylint`와 `flake8`이 활성화된 경우, 둘 다 수정된 모든 `python` 파일에서는 실행되지만, 수정된 `markdown` 파일에서는 실행되지 않습니다.
  - 모든 린트 액션에는 상응하는 _업스트림_ 린트 액션도 있습니다, 즉, 린트는 업스트림 버전에서도 파일의 업스트림 버전에서도 실행되므로 저장소에 이미 어떤 이슈가 있는지 확인할 수 있습니다.
- 캐시되지 않은 린트 액션을 실행합니다.
- 어떤 린트 이슈가 새 이슈인지, 기존 이슈인지, 수정된 이슈인지 확인합니다.

## Linter Types

### SARIF

`output: sarif` 린터는 [Static Analysis Results Interchange Format](https://docs.oasis-open.org/sarif/sarif/v2.0/sarif-v2.0.html)으로 진단을 생성합니다:

```json
{
  "$schema": "https://raw.githubusercontent.com/oasis-tcs/sarif-spec/master/Schemata/sarif-schema-2.1.0.json",
  "version": "2.1.0",
  "runs": [
    {
      "results": [
        {
          "level": "warning",
          "locations": [
            {
              "physicalLocation": {
                "artifactLocation": {
                  "uri": "/dev/shm/sandbox/detekt_test_repo/example.kt"
                },
                "region": {
                  "startColumn": 12,
                  "startLine": 18
                }
              }
            }
          ],
          "message": {
            "text": "클래스는 equals를 재정의할 때 항상 hashCode를 재정의해야 하며, 그 반대의 경우도 마찬가지입니다.."
          },
          "ruleId": "detekt.potential-bugs.EqualsWithHashCodeExist"
        }
      ],
      "tool": {
        "driver": {
          "downloadUri": "https://github.com/detekt/detekt/releases/download/v1.19.0/detekt",
          "fullName": "detekt",
          "guid": "022ca8c2-f6a2-4c95-b107-bb72c43263f3",
          "informationUri": "https://detekt.github.io/detekt",
          "language": "en",
          "name": "detekt",
          "organization": "detekt",
          "semanticVersion": "1.19.0",
          "version": "1.19.0"
        }
      }
    }
  ]
}
```

### LSP JSON

`output: lsp_json`은 출력 문제를 [Language Server Protocol](https://microsoft.github.io/language-server-protocol/specification#diagnostic) JSON으로 린터링합니다.

```json
[
  {
    "message": "Not formatted correctly. Missing owner",
    "code": "missing-owner",
    "severity": "Error",
    "range": {
      "start": {
        "line": 12,
        "character": 8
      },
      "end": {
        "line": 12,
        "character": 12
      }
    }
  },
  {
    "message": "TODO is assigned to someone not listed in this project",
    "code": "unknown-user",
    "severity": "Warning",
    "range": {
      "start": {
        "line": 37,
        "character": 0
      },
      "end": {
        "line": 37,
        "character": 14
      }
    }
  }
]
```

### Pass/Fail Linters

`output: pass_fail` 린터는 둘 중 하나를 찾습니다:

- 파일에 문제가 없거나, `exit_code=0`으로 종료하여 표시되거나, 또는
- 파일에서 단일 파일 수준 문제를 발견하고, 그 메시지는 `stdout`으로 표시되며, exiting\로 표시됩니다.
  로 표시됩니다.

> 참고: `exit_code=1`로 종료하지만 `stdout`에 아무것도 쓰지 않으면 린터 도구 실패로 간주됩니다.
>
> 참고: `pass_fail` 린터에는 `success_codes: [0, 1]`

### 정규식

`output: regex` 린터는 사용자 정의 정규식 및 명명된 캡처 그룹으로 구문 분석할 수 있는 출력을 생성합니다. 정규식은 `parse_regex` 필드에 지정됩니다.

`regex`는 다음과 같은 명명된 캡처 그룹에 대해 린터 출력에서 문자열 캡처를 지원합니다:

- `path`: 파일 경로(필수)
- `line`: 줄 번호
- `col`: 열 번호
- `severity`: `allow`, `deny`, `disabled`, `error`, `info`, `warning` 중 하나
- `code`: 린터 진단 코드
- `message`: 설명

예를 들어, 출력은

```log
.trunk/trunk.yaml:7:81: [error] line too long (82 > 80 characters) (line-length)
```

정규식을 사용하여 구문 분석할 수 있습니다.

```r
((?P<path>.*):(?P<line>\d+):(?P<col>\d+): \[(?P<severity>.*)\] (?P<message>.*) \((?P<code>.*)\))
```

입력하면 다음과 같은 `trunk` 진단이 표시됩니다:

```log
7:81  high    line too long (82 > 80 characters)      regex-linter/line-length
```

같은 이름의 캡처 그룹이 여러 개 지정되어 있는 경우 비어 있지 않은 캡처가 우선적으로 사용됩니다. 비어 있지 않은 캡처가 여러 개 있으면 린터 오류가 발생합니다. 출력의 특성에 맞게 정규식을 적절히 조정하세요.

> 참고: 사용자 지정 정규식 작성에 대한 자세한 내용은 [re2](https://github.com/google/re2/wiki/Syntax)를 참조하세요. 더 복잡한 정규식을 사용하려면 yaml 구성에 추가 이스케이프 문자가 필요할 수 있습니다.

### Arcanist

Arcanist 형식을 사용하여 JSON을 출력할 수도 있습니다.

```json
[
  {
    "Char": 1,
    "Code": "missing_copyright",
    "Description": "Message about things\nMaybe contain multiple lines and web\nlinks\nhttps://website.com/notice-about-stuff\n",
    "Line": 1,
    "Name": "Incorrect (or missing) copyright notice",
    "OriginalText": "",
    "Path": "somefile.py"
  }
]
```

### Formatters

`output: rewrite` 린터는 형식이 지정된 파일 버전을 `stdout`에 씁니다; 이것은 자동 수정이 되어 `trunk`가 사용자에게 적용하라는 메시지를 표시하거나(`trunk check`가 기본적으로 수행하는 작업) 자동으로 적용할 수 있습니다(`trunk check --fix`또는 `trunk fmt`를 사용하는 경우).

예를 들어, 줄 끝을 정규화하기 위해 린터를 사용하려는 경우 이렇게 할 수 있습니다:

```yaml
lint:
  definitions:
    - name: no-carriage-returns
      files: [ALL]
      commands:
        - output: rewrite
          formatter: true
          command: sed s/\r// ${target}
          success_codes: [0]
```

`format: true`를 설정하면 `trunk fmt`가 이 린터를 실행합니다.

## Configuration Options

사용자 정의 린터를 정의할 때 `trunk`는 린터 명령의 출력뿐만 아니라 이를 호출하는 방법, 확인할 파일을 지정하는 방법 등과 같은 세부 사항도 알아야 합니다. 다음 몇 섹션에서는 이를 구성하는 데 사용하는 옵션과 그 방법에 대해 설명합니다:

더 자세한 내용은 [`trunk.yaml`의 JSON 스키마](https://static.trunk.io/pub/trunk-yaml-schema.json)를 참조하세요.

### Applicable filetypes

어떤 파일에서 어떤 린터를 실행할지 결정하기 위해(즉, 린트 액션 집합을 계산하기 위해), `trunk`는 모든 린터가 `files`에 적용하는 파일 형식 집합을 정의해야 합니다.

미리 정의된 여러 파일 형식(예: `c++-header`, `gemspec`, `rust`, 최신 목록은 [구성 스키마](https://static.trunk.io/pub/trunk-yaml-schema.json)를 참조)이 있지만, 사용자가 직접 파일 형식을 정의할 수도 있습니다. 다음은 `python` 파일 유형을 정의하는 방법입니다:

```yaml
lint:
  files:
    - name: python
      extensions:
        - py
        - py2
        - py3
      shebangs:
        - python
        - python3
```

이것은 `trunk`에게 다음 조건 중 하나와 일치하는 파일을 `python` 파일로 간주해야 한다고 알려줍니다:

- 확장자가 `.py`, `.py2` 또는 `.py3` 중 하나(예: `lib.py`).
- 쉘방이 `python` 또는 `python3` 중 하나(예: `#!/usr/bin/env python3`).

### Command

`trunk`가 어떤 파일에서 어떤 린터를 실행할지 파악하면, `trunk`는 `run` 필드에 제공된 템플릿을 확장하여 린터를 호출할 인수를 결정합니다. 다음은 Kotlin 린터 중 하나인 `detekt`의 모습입니다:

```yaml
lint:
  definitions:
    - name: detekt
      # ...
      commands:
        - output: sarif
          run:
            detekt-cli --build-upon-default-config --config .detekt.yaml --input ${target} --report
            sarif:${tmpfile}
```

이 명령 템플릿에는 `trunk`가 `detekt`를 실행하는 데 필요한 모든 정보가 포함되어 있으며, `trunk`가 `detekt`의 출력을 이해할 수 있습니다.

이 명령 템플릿의 일부 필드에는 `${}` 토큰이 포함되어 있습니다. 이 토큰은 `command`가 템플릿인 이유이며 실행 시 실행 중인 린트 작업의 컨텍스트 내에서 해당 변수의 값으로 대체됩니다.

| Variable          | Description                                                         |
| ----------------- | ------------------------------------------------------------------- |
| `${workspace}`    | 리포지토리의 루트 경로                                              |
| `${target}`       | 검사할 파일의 경로(`${작업 영역}` 기준)                             |
| `${linter}`       | 린터가 다운로드된 디렉터리의 경로입니다.                            |
| `${runtime}`      | 런타임(예: `node`)이 다운로드된 디렉터리 경로입니다.                |
| `${upstream-ref}` | 신규/기존/수정 이슈를 계산하는 데 사용되는 업스트림 git 커밋입니다. |
| `${plugin}`       | 플러그인 리포지토리의 루트 경로                                     |

### Input

`target` 필드는 입력 파일이 주어지면 이 린터가 실행될 경로를 지정합니다. 리터럴 `.`와 같이 전체 리포지토리에 대해 린터를 실행할 수 있습니다. 또한 다양한 대체를 지원합니다:

| Variable                         | Description                                                                                                                           |
| -------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| `${file}`                        | 입력 파일입니다.                                                                                                                      |
| `${parent}`                      | 파일이 들어 있는 폴더입니다.                                                                                                          |
| `${parent_with(<name>)}`         | 저장소 루트로 이동하여 `<이름>`이 포함된 첫 번째 폴더를 찾습니다. `<이름>`을 찾지 못하면 린터를 실행하지 않습니다.                    |
| `${root_or_parent_with(<name>)}` | 리포지토리 루트를 향해 걸어 올라가 `<이름>`이 포함된 첫 번째 폴더를 찾습니다. `<이름>`을 찾을 수 없으면 리포지토리 루트로 평가합니다. |

`target`이 지정되지 않으면 기본값은 `${file}`이 됩니다.

이 대상은 `run` 필드에서 `${target}`으로 참조할 수 있습니다.

```yaml
lint:
  definitions:
    - name: noop
      files: [ALL]
      commands:
        - output: rewrite
          formatter: true
          run: cat ${target}
```

또는 `stdin`을 통해 `stdin: true`를 지정합니다:

```yaml
lint:
  definitions:
    - name: noop
      files: [ALL]
      commands:
        - output: rewrite
          formatter: true
          run: cat -
          stdin: true
```

> 정보: `stdin`을 통해 입력을 받는 린터는 파일 경로로 진단을 생성하기 위해 여전히 파일 경로를 알고 싶어할 수 있습니다. 이런 경우에도 `run`에서 `${target}`을 사용할 수 있습니다.

### Output

`trunk`가 린터에서 기대하는 출력 형식은 [`linter-types`](. /#linter-types)에 의해 결정됩니다.

**`STDOUT`, `STDERR` 또는 `TMP_FILE`**

`trunk`는 일반적으로 `stdout`에 결과를 출력하는 것을 기대하지만, 다른 출력 메커니즘도 지원합니다:

| `read_output_from` | Description                                                                |
| ------------------ | -------------------------------------------------------------------------- |
| `stdout`           | 표준 출력.                                                                 |
| `stderr`           | 표준 에러.                                                                 |
| `tmp_file`         | `command`에 `${tmpfile}`이 지정되었다면, 생성된 `${tmpfile}`의 경로입니다. |

### **Exit codes**

린터는 결과를 분류하기 위해 종종 다른 종료 코드를 사용합니다. 예를 들어, [`markdownlint`](https://github.com/igorshubovych/markdownlint-cli#exit-codes)는 `0`을 사용하여 문제가 발견되지 않았음을 나타내고, `1`을 사용하여 도구가 성공적으로 실행되었지만 문제가 발견되었음을 나타내며, `2`, `3`, `4`를 사용하여 도구 실행에 실패했음을 나타냅니다.

`trunk`는 린터에 대해 `success_codes` 또는 `error_codes` 지정을 지원합니다:

- `success_codes`가 지정되면, `trunk`는 성공적인 린터 호출(문제를 찾거나 찾지 못할 수도 있습니다) 지정된 `success_codes` 중 하나를 반환합니다;
- `error_codes`가 지정되면, `trunk`는 성공적인 린터 호출이 종료 코드 중 하나를 반환할 것으로 기대합니다.

예를 들어, `markdownlint`는 `success_codes: [0, 1]`가 구성에 있습니다.

### **Working directory**

`run_from`은 린터 명령이 실행되는 디렉터리를 결정합니다.

|                                         |                                                                                  |
| :-------------------------------------- | :------------------------------------------------------------------------------- |
| `run_from`                              | 설명                                                                             |
| `<path>` (`.` by default)               | 실행할 명시적 경로                                                               |
| `${parent}`                             | 대상 파일의 상위 파일, 예: `foo/bar/hello.txt`의 경우 `foo/bar`입니다.`          |
| `${root_or_parent_with(<file>)}`        | 지정한 파일을 포함하는 가장 가까운 상위 디렉터리                                 |
| `${root_or_parent_with_dir(<dir>)}`     | 지정한 디렉터리를 포함하는 가장 가까운 상위 디렉터리                             |
| `${root_or_parent_with_regex(<regex>)}` | 지정한 정규식과 일치하는 파일 또는 디렉터리를 포함하는 가장 가까운 상위 디렉터리 |
| `${target_directory}`                   | 대상 파일과 동일한 디렉토리에서 린터를 실행하고 대상을 `.`로 변경합니다.         |
| `${compile_command}`                    | `compile_commands.json`이 있는 디렉토리에서 실행합니다.                          |

### **Limiting concurrency**

Trunk가 동시에 린터를 호출하는 횟수를 제한하려면 `maximum_concurrency` 옵션을 사용할 수 있습니다. 예를 들어, `maximum_concurrency: 1`을 설정하면 Trunk가 동시에 두 개 이상의 린터 인스턴스를 실행하지 못하도록 제한합니다.

### **Environment variables**

기본적으로 `trunk`는 부모 셸의 환경 변수 _없이_ 린터를 실행합니다. 그러나 대부분의 린터는 최소한 몇 가지 환경 변수를 설정해야 하므로 `trunk`는 `environment`을 사용하여 환경 변수를 지정할 수 있습니다(예: `ktlint`의 `environment`은 다음과 같습니다):

```yaml
lint:
  definitions:
    name: ktlint
    # ...
    environment:
      - name: PATH
        list: ["${linter}"]
      - name: LANG
        value: en_US.UTF-8
```

대부분의 `environment` 항목은 `name` 및 `value` 키가 있는 맵이며, 이는 `name=value` 환경 변수가 됩니다. `PATH`의 경우 `list`를 지정할 수 있으며, 이 경우 `:`로 항목을 연결합니다.

`environment`에 대해서는 [`command`](./#command)에서와 동일한 템플릿 구문을 사용합니다.

## Tools

`tools` 섹션을 사용하여 린터가 실행하는 데 사용하는 Trunk 구성 바이너리를 지정할 수 있습니다. `tools` 키는 도구 이름을 나타내는 문자열 목록을 지정해야 합니다. 도구 종속성에는 두 가지 종류가 있으며, 아래에서 차례로 설명합니다. 도구 설정 방법에 대한 자세한 내용은 [도구 구성](./Configuration.md) 페이지를 참조하세요.

이 방법은 저장소 사용자가 `trunk check` 컨텍스트 외부에서 편리하게 린터 바이너리를 실행할 수 있기 때문에 린터를 정의하고 버전을 관리하는 데 선호되는 방법입니다.

### Eponymous Tool Dependencies

다음은 도구가 린터 이름과 일치하는 경우의 예시입니다:

```yaml
tools:
  definitions:
    - name: pylint
      runtime: python
      package: pylint
      shims: [pylint]
      known_good_version: 2.11.1
lint:
  definitions:
    - name: pylint
      files: [python]
      commands:
        - name: lint
          # Custom parser type defined in the trunk cli to handle pylint's JSON output.
          output: pylint
          run: pylint --exit-zero --output ${tmpfile} --output-format json ${target}
          success_codes: [0]
          read_output_from: tmp_file
          batch: true
          cache_results: true
      tools: [pylint]
      suggest_if: config_present
      direct_configs:
        - pylintrc
        - .pylintrc
      affects_cache:
        - pyproject.toml
        - setup.cfg
      issue_url_format: http://pylint-messages.wikidot.com/messages:{}
      known_good_version: 2.11.1
      version_command:
        parse_regex: pylint ${semver}
        run: pylint --version
```

이 경우 도구 이름(`pylint`)이 린터의 이름과 일치하므로 \_eponymous tool\_가 됩니다. 시조 도구는 린터와 별도로 정의해야 하지만 린터 버전에서 암시적으로 활성화해야 합니다. 원하는 경우 시조 도구를 명시적으로 활성화할 수 있지만, 그 버전은 린터의 버전과 동기화되어야 한다는 점에 유의하세요.

### Additional Tool Dependencies

린터가 동일한 이름이 아닌 다른 도구에 종속되는 시나리오, 즉 \_additional tool dependency\_을 가질 수도 있습니다. 아래에서 예를 들어보겠습니다:

```yaml
tools:
  definitions:
    - name: terragrunt
      known_good_version: 0.45.8
      download: terragrunt
      shims:
        - name: terragrunt
          target: terragrunt
lint:
  definitions:
    - name: terragrunt
      tools: [terragrunt, terraform]
      known_good_version: 0.45.8
      files: [hcl]
      suggest_if: never
      environment:
        - name: PATH
          list: ["${linter}"]
      commands:
        - name: format
          output: rewrite
          run: terragrunt hclfmt ${target}
          success_codes: [0]
          sandbox_type: copy_targets
          in_place: true
          formatter: true
          batch: true
      version_command:
        parse_regex: terragrunt v${semver}
        run: terragrunt -version
```

이 시나리오에서 `terraform`은 추가 도구 종속성이며, `terragrunt`는 `$PATH`에 있어야 합니다. 도구가 추가 종속 요소인 경우 명시적으로 활성화하고 린터와 독립적으로 버전을 지정해야 합니다. 즉, `tools.enabled` 섹션에 나열되어야 합니다.

### Downloads

**(참고: 린터를 지정하는 이 방법은 계속 지원되지만, [**위**](./Custom%20Linters.md#tools)에서 지정된 대로 `tools`를 사용하는 것이 좋습니다.)**

커스텀 린터에 별도의 릴리스 프로세스가 있는 경우(즉, 리포지토리에 커밋되지 않은 경우) 다음과 같이 `trunk`에게 다운로드 방법을 알려줄 수 있습니다:

```yaml
lint:
  downloads:
    - name: lorem-linter
      # the default version to download; overridden by the version in `enabled`
      version: 4.0.1
      executable: true
      downloads:
        - os: linux
          cpu: x86_64
          url: https://github.com/my-org/my-repo/releases/download/${version}/lorem-darwin-x86-64
        - os: macos
          cpu: x86_64
          url: https://github.com/my-org/my-repo/releases/download/${version}/lorem-linux-x86-64
    - name: ipsum-linter
      # 다운로드할 기본 버전으로, `enabled`의 버전으로 재정의됩니다.
      version: 0.1.1
      downloads:
        - os: linux
          cpu: x86_64
          url: https://github.com/my-org/my-repo/releases/download/${version}/ipsum-darwin-x86-64.tar.gz
          strip_components: 2
        - os: macos
          cpu: x86_64
          url: https://github.com/my-org/my-repo/releases/download/${version}/ipsum-linux-x86-64.tar.gz
          strip_components: 2
  definitions:
    - name: lorem-linter
      files: [javascript, typescript]
      download: lorem-linter
    - name: ipsum-linter
      files: [rust]
      download: ipsum-linter
  enabled:
    - lorem-linter@4.0.2
    - ipsum-linter@0.1.6
```

이것은 `trunk`에게 `lorem-linter`의 경우:

- `javascript` 및 `typescript` 파일에 대해 버전 `4.0.2`를 실행하고 싶다고 알려줍니다,
- 지정된 URL(`${version}`을 `4.0.2`로 대체하여 확장)에서 macOS 및 Linux에서 사용할 수 있습니다.
- `executable: true`가 설정되어 있으므로 다운로드는 단일 실행 바이너리로 구성됩니다;

`ipsum-linter`의 경우

- `rust` 파일에서 버전 `0.1.6`을 실행하려고 합니다,
- 지정된 URL(`${version}`을 `0.1.6`으로 대체하여 확장)에서 macOS 및 Linux에서 사용할 수 있습니다.
- 다운로드는 압축된 아카이브이고, 린터 바이너리는 `strip_components: 2` 디렉토리에 있으며, `trunk`는 압축되지 않은 아카이브에서 린터를 자동으로 추출하고 압축을 풀어야 합니다.

### Download via package manager

`gem install`, `go get`, `npm install` 또는 `pip install`을 통해 린터를 다운로드할 수 있는 경우 `runtime`과 `package` 키를 지정할 수 있습니다:

```yaml
lint:
  definitions:
    - name: fizz-buzz
      files: [javascript]
      # npm install fizz-buzz
      runtime: node
      package: fizz-buzz
```

이제 `~/.cache/trunk/linters/fizz-buzz`에 비공개 디렉터리가 생성되고 거기에 `npm install fizz-buzz`가 설치됩니다. 평소처럼 `trunk.yaml`에서 `fizz-buzz@1.2.3`을 통해 다른 버전의 패키지를 참조할 수 있습니다.

> 참고: 이러한 다운로드는 사용자 컴퓨터에 설치한 런타임이 아니라 `trunk`가 설치하는 지정된 런타임의 _hermetic_ 버전을 >사용합니다.

### `run_when`

Lint 세션은 항상 다음 세션 유형 중 정확히 하나에 연결됩니다:

- `ci`: `CI` 실행(예: `trunk check --ci`)에 의해 트리거되거나 GitHub 작업 내부에서 실행될 때 트리거됩니다,
- `cli`: `trunk check`를 실행하는 사람이나 스크립트에 의해 트리거됩니다,
- `lsp`: VSCode에 의해 트리거됨, 또는
- `monitor`: 백그라운드 린팅에 의해 트리거됩니다.

`run_when`을 사용하여 린터를 실행할 세션 유형을 지정할 수 있습니다(예: CI 중에 항상 린터를 비활성화하려면):

```yaml
lint:
  definitions:
    - name: fizz-buzz
      run_when: [cli, lsp, monitor]
```
