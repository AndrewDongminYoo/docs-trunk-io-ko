---
description: enabling and writing your own actions
---

# Configuration

작업은 [`trunk.yaml`](./Trunk%20YAML.md)의 `actions` 섹션에서 정의 및 활성화됩니다.

다음은 `trunk.yaml`의 action 섹션의 예입니다. 동작에 대한 해결된 구성이 어떻게 보이는지 궁금하다면 `trunk config print`를 실행하세요.

```yaml
actions:
  enabled:
    - trunk-announce
    - trunk-upgrade-available
    - npm-install
    - seed-database
    - custom-git-hook
    - login
  definitions:
    - id: npm-install
      triggers:
        - files: [package.json]
      run: npm install
    - id: seed-database
      triggers:
        - schedule: 24h
      run: python3 seed_database.py
      runtime: python
      run_from: utils
      packages_file: requirements.txt
    - id: custom-git-hook
      triggers:
        - git_hooks: [pre-push, pre-commit]
      run: my_script.sh
    - id: login
      run: my_complicated_login_script.sh
      interactive: true
```

## Action Definitions

이제 나만의 액션을 만드는 과정을 살펴보겠습니다.

액션에는 `id`와 `run` 명령이 필요합니다.

이 명령은 암시적으로 작업 영역을 기준으로 실행되지만, 하위 디렉터리에서 실행하고 싶다면 `run_from`을 지정할 수도 있습니다.

### Runtime management

액션 실행을 샌드박스화하여 사용자가 런타임을 제어할 수 있습니다. `runtime` 및 `packages_file`을 지정하여 이를 수행할 수 있습니다.

기본 제공 런타임(`node`, `python`, ...) 중 하나를 지정하거나 사용자가 정의한 시스템 런타임을 지정할 수 있습니다. 자세한 내용은 [런타임 문서](./Trunk%20YAML.md#runtimes)를 참조하세요.

`python` 및 `node` 런타임의 경우 `requirements.txt` 또는 `package.json`과 같은 요구 사항 파일을 설치할 수 있는 기능을 추가로 제공합니다.

## Triggers

`trunk 실행 <action_id> <args>` 또는 `trunk 액션 실행 <action_id> <args>`를 실행하여 수동으로 액션을 실행할 수 있습니다. 그러나 특정 이벤트에 대한 응답으로 액션이 실행되도록 트리거 집합을 제공할 수도 있습니다. 이러한 트리거는 아래에 설명되어 있습니다.

### Time-based triggers

일정에 따라 백그라운드에서 작업을 실행하는 기능을 제공합니다.

`triggers` 아래에 하나 이상의 `schedule` 항목을 추가할 수 있습니다. 예를 들어

```yaml
id: my-action
triggers:
  - schedule: 1d
```

`schedule` 항목은 [여기](https://pkg.go.dev/time#ParseDuration)에 지정된 기간 형식이어야 합니다. 작업은 `duration`당 한 번 실행됩니다.

이것은 일정을 객체로 지정하는 단축키입니다. 다음과 같이 작성할 수도 있습니다.

```yaml
id: my-action
triggers:
  - schedule:
      interval: 1d
```

Trunk Daemon의 수명에 따라 액션이 지정된 기간보다 더 자주 실행될 수 있습니다.

비슷한 스케줄을 가진 다른 액션과 실행 시차를 두려면 `delay` 필드를 사용하면 됩니다.

```yaml
id: my-action
triggers:
  - schedule:
      interval: 1d
      delay: 1h
```

cron 구문을 사용할 수도 있습니다:

```yaml
id: my-action
 triggers:
    # run every 2 hours
    - schedule: "0 0 */2 * * ?"
```

또는 이와 동등한 값입니다:

```yaml
id: my-action
 triggers:
    # run every 2 hours
    - schedule:
        cron: "0 0 */2 * * ?"
```

### File-based triggers

파일 편집에 따라 자동으로 작업을 실행하는 기능을 제공합니다.

정확한 파일 이름 또는 글로브를 제공할 수 있습니다.

```yaml
id: my-action
triggers:
  - files: [foo.txt, bar/**]
```

이 경우 `foo.txt`가 편집(또는 생성)되거나 `bar` 안에 있는 파일이 편집 또는 생성되면 `my-action`이 실행됩니다.

참고: 작업 영역 내부의 파일에 대한 파일 트리거만 제공합니다.

### Git hooks

Trunk를 구성하여 Git 훅을 관리할 수도 있습니다. 이에 대한 자세한 내용은 [Git 훅 참조](./Git%20Hooks.md)에서 확인할 수 있습니다.

## Interactivity

동작이 인터랙티브한 것으로 표시된 경우 `stdin`에서 읽을 수 있습니다(동작에 `interactive: true` 정의). 참고 - 이 기능은 Git 훅과 수동으로 실행되는 작업에만 사용할 수 있으며, 파일 트리거 및 예약된 작업은 백그라운드에서 실행되므로 실행과 상호작용할 수 없습니다.
