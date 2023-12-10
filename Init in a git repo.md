# git 리포지토리에서 초기화

git 저장소에서 `trunk`를 초기화하는 것은 실행하는 것만큼이나 간단합니다:

```sh
trunk init
```

이렇게 하면 리포지토리를 스캔하여 `.trunk/trunk.yaml` 파일이 생성되며, 이 파일은 [Trunk Check](./Check.md)가 권장하는 모든 린터, 포맷터 및 보안 분석기를 사용할 수 있습니다.

보안에 민감한 사용자는 [Trunk Launcher](./Components.md#trunk-launcher)가 CLI의 출처를 확인하는 데 사용할 CLI의 서명도 기록할 수 있습니다:

```sh
trunk init --lock
```

## 구성 조정

`trunk`는 `trunk.yaml` 파일을 통해 완전히 제어됩니다. 예를 들어 `check` 도구를 사용하지 않는 경우 파일에서 `lint` 섹션을 안전하게 제거할 수 있습니다.

## 싱글플레이어 모드

리포지토리 내에서 `trunk`를 실행하고 싶지만 팀 전체에 배포할 준비가 되지 않은 경우 싱글플레이어 모드라고 하는 방식으로 실행할 수 있습니다.

싱글플레이어 모드에서는 `.trunk` 디렉터리가 `.git/info/exclude`에 나열되어 git이 그 내용을 무시하게 됩니다. vscode 확장자에 의해 Trunk가 자동으로 초기화되면 이 모드에서 시작됩니다. Trunk Init 명령으로 명시적으로 이 모드를 초기화할 수도 있습니다. 언제든지 싱글플레이어 모드를 켜거나 끄려면 다음 두 명령어를 사용하면 됩니다:

```sh
# 싱글플레이어 모드를 켭니다.
trunk config hide

# 싱글플레이어 모드를 끕니다.
trunk config share
```

## 탐지된 도구만 활성화

`trunk init`은 `--only-detected-formatters`와 `--only-detected-linters` 플래그를 지원합니다. 이 플래그는 각각 사용자가 이미 사용 중인 것으로 감지된 도구만 활성화하도록 `trunk init`을 제한합니다.
