# Git Hooks

Trunk supports triggering actions on all [githooks](https://git-scm.com/docs/githooks)

## Features

- `git` 훅을 버전 관리로 원활하게 가져오세요. `git` 훅은 수동 설치가 필요하고 나머지 코드와 함께 버전 관리가 쉽지 않기 때문에 조직에 큰 골칫거리가 될 수 있습니다.
- Trunk의 강력한 샌드박싱 및 환경 관리 기능을 활용하여 복잡한 bash 스크립트를 처리하는 대신 원하는 프로그래밍 언어와 런타임을 사용하여 훅을 작성하고 실행할 수 있습니다.

## Manual Installation

```sh
trunk git-hooks sync
```

## Automatic Installation

Trunk는 `trunk.yaml`에 git 이벤트에서 트리거되는 작업이 활성화되어 있는 경우 자동으로 `githook`을 설치하고 관리를 시작합니다.

## Triggering an action from a githook

[Plugins Repo](https://github.com/trunk-io/plugins)에서 `git-lfs` 액션을 구현하는 방법을 예로 살펴보겠습니다.

### Definition

```yaml
- id: git-lfs
  display_name: Git LFS
  description: Git LFS hooks
  run: git lfs "${hook}" "${@}"
  triggers:
    - git_hooks: [post-checkout, post-commit, post-merge, pre-push]
```

### Template resolution

[git](https://git-scm.com/docs/githooks)에 설명된 대로 각 깃훅은 작업의 `run` 항목에서 참조할 수 있는 다양한 수의 매개변수를 생성합니다.

다음과 같은 특수 변수는 git 이벤트에 반응할 때 템플릿 확인에 사용할 수 있습니다:

| Variable                      | Description                                                     |
| ----------------------------- | --------------------------------------------------------------- |
| `${hook}`                     | Hook that triggered this action (e.g. `pre-commit`, `pre-push`) |
| `${1}`,`${2}`, `${3}`, etc... | Positional parameters passed by `git` to the hook               |
| `${@}`                        | All parameters passed to the hook                               |

### Interactivity

```yaml
interactive: true
```

`interactive`를 true로 설정하면 대화형 터미널에서 깃훅 액션을 실행할 수 있습니다. 이를 통해 사용자 입력에 반응하는 더 복잡한 훅을 작성할 수 있습니다.

### Testing a `githook` action

다음 명령은 githook 이벤트를 시뮬레이션하고 제공된 훅에 대해 활성화된 모든 액션을 정의한 순서대로 실행합니다.

```sh
trunk git-hooks callback <hook> -- <args>
```

또는 작업이 활성화되면 `git`을 호출하고 실제 `git`에서 제공된 데이터로 디버깅할 수 있습니다. 이 방법이 더 쉬운 경우가 있는데, 일부 git 매개변수는 txt 파일 등을 가리키고 수동 테스트를 통해 이러한 형식을 만드는 것이 까다로울 수 있기 때문입니다.

### Debugging a `githook` action

호출을 통해 `git` 이벤트에 의해 트리거되는 액션을 관찰할 수 있습니다:

```sh
trunk actions history <action-name>
```

지정된 작업의 타임스탬프를 포함하여 최근 10개의 실행을 인쇄합니다. \\

<figure><img src="../assets/image (1) (1) (1) (1).png" alt=""><figcaption><p>trunk actions history for git-lfs action</p></figcaption></figure>

## Uninstalling

`trunk.yaml`에서 githook에 의해 트리거되는 모든 작업을 제거하고 다음을 실행합니다.

```sh
git config --unset core.hooksPath
```

## Trunk Announce

커밋에 다른 조직과 공유해야 할 중요한 정보가 포함되어 있나요? 이제 커밋 메시지 첫 줄에 `/trunk announce`를 포함하면 다른 조직과 쉽게 공유할 수 있습니다(조직에서 커밋 메시지를 압축하는 경우 PR 설명에 넣어야 합니다). 해당 줄의 추가 텍스트는 선택적 제목이 되고, 커밋 메시지의 나머지 텍스트는 커밋 본문이 됩니다(둘 다 선택 사항이지만 제목이나 본문 중 하나는 필수입니다). 그러면 다른 사용자가 끌어오거나 리베이스할 때 이 내용이 표시됩니다.

로컬에서 커밋이 어떻게 표시되는지 확인하려면 원하는 메시지로 커밋을 만든 다음 `trunk show-announcements since HEAD~1`을 실행하면 됩니다(`trunk show-announcements since <ref>`는 제공된 참조 이후의 모든 Trunk 알림을 표시합니다).

Trunk 알림을 사용하려면 `trunk actions enable trunk-announce`를 실행하면 됩니다.
