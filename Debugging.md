# Debugging

특정 파일에서 린터가 실패하는 이유를 진단하거나 도구에서 보이는 문제를 더 쉽게 재현하기 위해 자세히 살펴봐야 할 때가 있습니다.

## Analyzing Linter Failures

린터가 실패하면 `Trunk`는 무엇이 잘못되었고 왜 실패했는지 이해하는 데 필요한 모든 정보가 포함된 린터 실패 보고서를 생성합니다. 예를 들어 `prettier`는 부적절한 형식의 HTML에서 실패합니다. 어떤 모습인지 살펴보겠습니다:

```log
trunk/hello_world.html:0:0
 0:0  failure  prettier error (details: .trunk/out/lYa9w.yaml)  prettier
```

이것은 `trunk`가 `trunk/hello_world.html`에서 `prettier`를 실행했을 때 실패했으며, 호출에 대한 전체 보고서를 `.trunk/out/lYa9w.yaml`에서 찾을 수 있다는 것을 알려줍니다. 이 보고서에는 다음이 포함됩니다:

1. 실행된 명령,
2. 환경(예: `cwd`, 환경 변수),
3. 호출의 `stdout`, `stderr`, 종료 코드, 4.
4. 명령어 재생.

이 모든 것을 조합하면 일반적으로 파일이나 도구를 수정하여 린터에서 유용한 결과를 얻기에 충분합니다.

```yaml
title: "prettier error"
report:
  - command: |
      /home/horton/.cache/trunk/linters/prettier/2.3.2-368f87d0e434ae207c0a3622371f91cc/node_modules/.bin/prettier --stdin-filepath trunk/hello_world.html
    environment:
      HOME: /home/horton
      PATH: /home/horton/.cache/trunk/linters/node/16.14.2/bin:/home/horton/.cache/trunk/linters/prettier/2.3.2-368f87d0e434ae207c0a3622371f91cc/node_modules/.bin
    run_from: /tmp/trunk-Bi6hWP
    rerun: env -i -C /tmp/trunk-Bi6hWP HOME=/home/horton
      PATH=/home/horton/.cache/trunk/linters/node/16.14.2/bin:/home/horton/.cache/trunk/linters/prettier/2.3.2-368f87d0e434ae207c0a3622371f91cc/node_modules/.bin
      /home/horton/.cache/trunk/linters/prettier/2.3.2-368f87d0e434ae207c0a3622371f91cc/node_modules/.bin/prettier
      --stdin-filepath trunk/hello_world.html
    exit_code: 2
    stdout: (none)
    stderr: |
      [error] trunk/hello_world.html: SyntaxError: Unexpected closing tag "head". It may happen when the tag has already been closed by another tag. For more info see https://www.w3.org/TR/html5/syntax.html#closing-elements-that-have-implied-end-tags (3:2)
      [error]   1 | <html>
      [error]   2 |  head>
      [error] > 3 |  </head>
      [error]     |  ^^^^^^^
      [error]   4 |  <body>
      [error]   5 |    <h1>Hello World<h1>
      [error]   6 |  </body>
```

## Debugging Linter Failures in CI

CI에서 trunk check가 실행되는 동안 린터 오류가 발생하면 다음과 같은 메시지가 표시됩니다:

<figure><img src="../assets/Screenshot 2023-10-16 at 5.09.29 PM.png" alt=""><figcaption></figcaption></figure>

이를 디버깅하려면 Github Actions 출력에서 린터 실패 보고서를 가져와야 합니다. "Details"를 클릭하면 다음과 같은 화면이 표시됩니다:

<figure><img src="../assets/Screenshot 2023-10-16 at 5.10.39 PM.png" alt=""><figcaption></figcaption></figure>

그런 다음 "트렁크-io에 대한 자세한 정보 보기"를 클릭합니다.

<figure><img src="../assets/Screenshot 2023-10-16 at 5.11.57 PM.png" alt=""><figcaption></figcaption></figure>

그러면 검사 실행 요약이 표시됩니다. CI 로그를 보려면 "\<귀하의 리포지토리 이름>/pull_request"를 클릭하고 맨 아래로 스크롤합니다.

<figure><img src="../assets/Screenshot 2023-10-16 at 5.12.23 PM.png" alt=""><figcaption></figcaption></figure>

그러면 발생한 실패 목록과 모든 린터 실패 보고서가 접을 수 있는 형태로 표시됩니다. 그런 다음 [린터 실패 분석하기](https://docs.trunk.io/check/debugging#analyzing-linter-failures)의 지침에 따라 문제를 해결할 수 있습니다.

## Actions

`trunk check`가 실행되면 동적 검사 작업 집합이 생성되어 병렬로 실행됩니다. 사용자 지정 통합을 개발하는 엔지니어는 `trunk check`가 특정 도구를 실행할 때 정확히 무엇을 하고 무엇을 보는지 자세히 살펴보는 것이 도움이 될 수 있습니다. `check` 실행의 출력에는 실행된 각 작업에 대한 실행 보고서와 캐시 히트 정보가 포함된 `ACTIONS` 섹션이 포함됩니다.

예를 들어 hello.py에서 수행된 모든 작업에 대한 보고서를 실행하려면 다음과 같이 하세요:

```sh
trunk check --verbose hello.py
```
