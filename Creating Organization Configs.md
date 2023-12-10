# Creating Organization Configs

조직 전체에서 Trunk Config을 표준화하기 위해 공개 Plugins Repo를 만들고 게시할 수 있습니다. 이 저장소에서는 새로운 린터 정의를 정의하고, 활성화된 린터와 작업을 지정하며, 심지어 [린터 구성 내보내기](./Import%20Export%20Linter%20Configs.md)도 할 수 있습니다.

플러그인 리포지토리를 만든 후에는 다른 리포지토리에서 소싱하여 조직 전체에 공유 구성을 적용할 수 있습니다. 우리 조직에서 이 작업을 수행하는 방법에 대한 예는 [configs repo](https://github.com/trunk-io/configs)를 참조하세요.

플러그인 구성 리포지토리에 있는 린터와 도구를 최신 상태로 유지하려면 `trunk 업그레이드 --apply-to=plugin.yaml`을 실행하여 [업그레이드](./Upgrade.md#플러그인-repos-and-user.yaml)를 적용해야 한다는 점에 유의하세요. 플러그인 변경 사항이 포함된 공개 GitHub 릴리스를 만든 후 다른 종속 리포지토리는 `trunk 업그레이드`를 실행할 때 이러한 변경 사항을 자동으로 선택합니다.

## Get started

코드베이스에서 할 일에 대해 경고하는 간단한 린터를 만드는 방법을 살펴봅시다.

먼저 새 Git 리포지토리를 만드는 것으로 시작하겠습니다:

```sh
PLUGIN_PATH=~/my-first-trunk-plugin
mkdir "${PLUGIN_PATH}" && cd "${PLUGIN_PATH}"
git init
```

그런 다음 `grep`와 `sed`를 사용하여 코드베이스에서 할 일을 찾을 수 있는 린터를 만드세요:

```sh
cat >plugin.yaml <<EOF
version: 0.1
lint:
  definitions:
    - name: todo-finder
      files: [ALL]
      commands:
        - output: parsable
          run: grep --with-filename --line-number --ignore-case todo ${target}
          success_codes: [0, 1]
          read_output_from: stdout
          parser:
            run: "sed -E 's/(.*):([0-9]+):(.*)/\\1:\\2:0: [error] Found todo in \"\\3\" (found-todo)/'"
EOF
```

이제 `trunk`가 설정된 리포지토리에서 이 린터를 켤 수 있습니다:

```sh
trunk plugins add my-first-plugin "${PLUGIN_PATH}"
trunk check enable todo-finder
```

이제 이 기능이 어떻게 작동하는지 보여드리기 위해 할 일이 있는 파일 몇 개를 `trunk check`해 보겠습니다:

```sh
trunk check $(git grep -li todo | head -n 10)
```

다음과 같은 내용이 표시됩니다:

```log
.eslintrc.yaml:19:0
  19:0  high  Found todo in "  # TODO(chris): Figure out why this causes a massive slowdown ... .trunk/dev-out/O1F.txt  local.todo-finder/found-todo
 101:0  high  Found todo in "  node/no-unpublished-import: off # TODO: do we want this?"                                local.todo-finder/found-todo
```

## Organizing your code

위에서 예로 든 예제에서는 린터의 소스 코드를 `plugin.yaml`에 넣었는데, 이는 예제로는 괜찮지만 그 이상의 용도로는 적합하지 않습니다. 앞서 만든 플러그인에서 `sed` 명령을 가져와서 셸 스크립트에 넣으면 됩니다:

```sh
#!/bin/bash
sed -E 's/(.*):([0-9]+):(.*)/\1:\2:0: [error] Found todo in \"\3\" (found-todo)/'"
```

> 팁: `trunk`가 실행할 수 있도록 `chmod u+x todo-finder-parser.sh`를 실행하는 것을 잊지 마세요!

실행하고 `todo-finder`의 정의를 지정하세요:

```yaml
version: 0.1
lint:
  definitions:
    - name: todo-finder
      files: [ALL]
      commands:
        - output: parsable
          run: grep --with-filename --line-number --ignore-case todo ${target}
          success_codes: [0, 1]
          read_output_from: stdout
          parser:
            run: ${plugin}/todo-finder-parser.sh
```

We can also go another step and push the entire linter definition into a shell script:

```sh
#!/bin/bash
grep --with-filename --line-number --ignore-case todo "${1}" | \
  sed -E 's/(.*):([0-9]+):(.*)/\1:\2:0: [error] Found todo in \"\3\" (found-todo)/'"
```

```yaml
version: 0.1
lint:
  definitions:
    - name: todo-finder
      files: [ALL]
      commands:
        - output: parsable
          run: ${plugin}/todo-finder.sh
          success_codes: [0]
```

자바스크립트나 파이썬으로 구문 분석기를 작성하는 방법 등 자세한 내용은 [커스텀 린터](./Custom%20Linters.md) 및 [커스텀 파서](./Custom%20Parsers.md) 문서를 참조하세요!

## Publishing your plugin

플러그인을 전 세계와 공유하려면 릴리스에 태그를 지정하고 GitHub, Gitlab 또는 기타 리포지토리 호스팅 서비스에 푸시하기만 하면 됩니다:

```sh
git add .
git commit "Create a TODO finder"
git tag -a v0.0.0 --message "Initial TODO finder release"
git remote add origin ${repo_url}
git push origin main v0.0.0
```

이제 인터넷에서 사용할 수 있으므로 누구나 플러그인을 실행하여 사용할 수 있습니다:

```sh
trunk plugins add --id=their-first-plugin ${repo_url} v0.0.0
```
