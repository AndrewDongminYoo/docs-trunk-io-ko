# Components

## Trunk Binary

Trunk binary는 `trunk 확인`, `trunk 작업` 및 로컬 `trunk 병합` 명령을 실행하는 데 필요한 모든 클라이언트 측 코드가 포함된 기본 실행 파일입니다.

## Trunk Launcher

Trunk Launcher는 사용자가 여러 버전의 `trunk` 사이를 쉽게 전환할 수 있도록 하는 배쉬 스크립트입니다. Trunk Launcher는 리포지토리의 고정된 버전의 `trunk`를 `.trunk/trunk.yaml`에서 읽고, 일치하는 바이너리를 다운로드하여 캐시에 저장하고 실행 파일을 실행합니다. 이렇게 하면 리포지토리의 모든 `trunk` 사용자가 동일한 버전을 사용할 수 있습니다.

런처는 머신(`/usr/local/bin`)에 설치하거나 리포지토리(예: `tools/` 디렉토리)에 커밋할 수 있습니다.

## Cache

`trunk`는 필요에 따라 컴포넌트를 다운로드하여 사용할 수 있도록 머신에 캐시합니다. 캐시에는 `trunk` 바이너리 자체(머신에서 필요에 따라 여러 버전 포함), trunk-managed 리포지토리에서 활성화된 린터/액션, 해당 도구를 실행하는 데 필요한 필수 런타임이 포함됩니다. trunk cache의 위치는 사용자가 구성할 수 있으며 다음 환경 변수를 따릅니다:

- `$TRUNK_CACHE`
- `$XDG_CACHE_HOME/trunk`
- `$HOME/.cache/trunk`
