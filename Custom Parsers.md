# Custom Parsers

거의 그대로 실행하고 싶은 명령이나 유틸리티가 있지만 Trunk가 기본적으로 구문 분석 방법을 이해하지 못하는 경우, 사용자 정의 구문 분석기를 삽입하여 출력을 `trunk`가 이해할 수 있는 형식으로 변환할 수 있습니다!

예를 들어, `grep`를 린터로 사용하고 싶지만 일치하는 항목에 더 많은 컨텍스트를 추가하고 싶다고 가정해 보겠습니다. 다음과 같이 사용자 정의 린터를 정의할 수 있습니다:

```yaml
lint:
  definitions:
    - name: todo-finder
      files: [ALL]
      commands:
        - output: regex
          parse_regex: "((?P<path>.*):(?P<line>\\d+):(?P<col>\\d+): \\[(?P<severity>.*)\\] (?P<message>.*) \\((?P<code>.*)\\))" # matches the parser run output
          run: grep --with-filename --line-number --ignore-case todo ${target}
          success_codes: [0, 1]
          read_output_from: stdout
          parser:
            run: "sed -E 's/(.*):([0-9]+):(.*)/\\1:\\2:0: [error] Found todo in \"\\3\" (found-todo)/'"
```

`trunk`가 구문 분석기에 대해 따르는 실행 모델은 다음과 같습니다:

- 린터의 `run` 필드를 실행하여 둘 중 하나를 주장합니다:
  - 린터의 종료 코드가 `success_codes`에 있거나, 또는 종료 코드가 `error_codes`에 있지 않다고 가정합니다;
- `parser.run`을 실행합니다,
  - 를 실행하고, `parser.run`에 `stdin`으로 공급된 린터 실행의 `read_output_from`을 사용합니다,
  - 구문 분석기의 종료 코드가 0이라고 주장한 다음
- `output`을 사용하여 구문 분석기의 `stdout`을 어떻게 구문 분석할지 결정합니다.

원하는 경우 `parser.runtime`을 [`node`](./Custom%20Parsers.md#node) 또는 [`python`](./Custom%20Parsers.md#python)으로 설정하여 자바스크립트나 파이썬으로 파서를 대신 작성할 수도 있습니다!

## Node

```yaml
lint:
  definitions:
    - name: todo-finder-node
      files: [ALL]
      commands:
        - output: parsable
          parse_regex: "((?P<path>.*):(?P<line>\\d+):(?P<col>\\d+): \\[(?P<severity>.*)\\] (?P<message>.*) \\((?P<code>.*)\\))" # matches the parser run output
          run: grep --with-filename --line-number --ignore-case todo ${target}
          success_codes: [0, 1]
          read_output_from: stdout
          parser:
            runtime: node
            run: ${workspace}/todo-finder-parser.js
```

```javascript
// #!/usr/bin/env node
"use strict";
let readline = require("readline");
let rl = readline.createInterface({ input: process.stdin });

rl.on("line", function (line) {
  let match = line.match(/(.*):([0-9]+):(.*)/);

  if (match) {
    let [_, path, line_number, line_contents] = match;
    console.log(
      `${path}:${line_number}:0: [error] Found todo in "${line_contents}" (found-todo)`,
    );
  }
});
```

`trunk`가 실행할 수 있도록 `chmod u+x todo-finder-parser.js`를 실행하는 것을 잊지 마세요!

## Python

```yaml
lint:
  definitions:
    - name: todo-finder-python
      files: [ALL]
      commands:
        - output: parsable
          parse_regex: "((?P<path>.*):(?P<line>\\d+):(?P<col>\\d+): \\[(?P<severity>.*)\\] (?P<message>.*) \\((?P<code>.*)\\))" # matches the parser run output
          run: grep --with-filename --line-number --ignore-case todo ${target}
          success_codes: [0, 1]
          read_output_from: stdout
          parser:
            runtime: python
            run: ${workspace}/todo-finder-parser.js
```

```python
#!/usr/bin/env python
import re, sys

for line in sys.stdin.readlines():
  match = re.match("(.*):([0-9]+):(.*)", line)
  if match:
    path, line_number, line_contents = match.groups()
    print(f"{path}:{line_number}:0: [error] Found todo in \"{line_contents}\" (found-todo)")

```

`trunk`가 실행할 수 있도록 `chmod u+x todo-finder-parser.py`를 실행하는 것을 잊지 마세요!
