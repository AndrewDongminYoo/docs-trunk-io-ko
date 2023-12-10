---
description: Learn how to enable and write your own actions.
---

# Actions

**Trunk Actions은 리포지토리를 위한 IFTTT라고 생각하시면 됩니다. 액션은 지정된** [**트리거**](./#트리거)가 발생할 때 실행되는 명령입니다.

가장 일반적인 Trunk Actions은 Trunk와 함께 기본 제공되며, `git commit`을 할 때마다 커밋을 보이지 않게 자동 포맷(`trunk fmt`)하고, `git push`를 할 때 `trunk check`를 실행하도록 트리거됩니다.

## Triggers

| trigger                      | description                                                      |
| :--------------------------- | :--------------------------------------------------------------- |
| time based                   | 일정에 따라 실행(한 시간에 한 번, 하루에 한 번, 일주일에 한 번)  |
| file modification            | 리포지토리의 파일이나 디렉토리가 변경될 때마다 실행.             |
| [githooks](./Git%20Hooks.md) | 나열된 깃훅 이벤트가 발생할 때마다 실행(예: 사전 커밋, 온-푸시). |
| manual                       | `trunk 실행 <작업 이름>`                                         |

## Command Line

| trunk actions <command> | description                                                  |
| :---------------------- | :----------------------------------------------------------- |
| `list`                  | 리포지토리에서 사용 가능한 모든 작업을 나열합니다.           |
| `history <action-name>` | 제공된 작업의 실행 기록을 인쇄합니다.                        |
| `enable <action-name>`  | 제공된 작업 활성화                                           |
| `disable <action-name>` | 제공된 액션 비활성화                                         |
| `run <action-name>`     | 제공된 액션 별칭을 수동으로 트리거: `trunk 실행 <액션 이름>` |

## Discovering Actions

[Trunk Plugin](https://github.com/trunk-io/plugins) 리포지토리는 리포지토리를 강화하는 데 도움이 되는 작업 모음과 함께 제공되며, 직접 작업을 작성하는 방법에 대한 예시를 제공합니다. 리포지토리 실행에서 활성화할 수 있는 작업 목록을 보려면 다음과 같이 하세요:

```sh
trunk actions list
```

<figure><img src="../assets/image (8).png" alt=""><figcaption><p></code>Trunk Actions 목록</code>에서 보고한 작업 목록</p></figcaption></figure>

## Enable/Disable Actions

Trunk는 `trunk.yaml`의 `enabled` 섹션에 나열된 작업만 실행합니다. 일부 기본 제공 액션은 기본적으로 활성화되어 있으며 비활성화 목록에 추가하여 명시적으로 비활성화할 수 있습니다. 언제든지 `trunk 작업 목록`을 실행하여 작업의 활성화 상태를 확인할 수 있습니다.

```yaml
actions:
  enabled:
    - trunk-announce
    - git-lfs
    - trunk-check-pre-push
    - trunk-fmt-pre-commit
    - trunk-cache-prune
    - trunk-upgrade-available
```
