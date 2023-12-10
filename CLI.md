---
description: Trunk CLI에 대해 자세히 알아보기
---

# CLI

## Getting Started

로컬에서 `trunk`를 사용하려면 다음을 통해 설치하세요:

```sh
curl https://get.trunk.io -fsSL | bash

# 프롬프트 응답 없이 설치하려면:
curl https://get.trunk.io -fsSL | bash -s -- -y

# OSX Homebrew로 설치하기
brew install trunk-io

# NPM으로 설치하기
npm install -D @trunkio/launcher

# PNPM으로 설치하기
pnpm add -D @trunkio/launcher

# Yarn으로 설치하기
yarn add -D @trunkio/launcher
```

## 무엇을 할 수 있나요?

Trunk CLI는 다음과 같은 용도로 사용할 수 있습니다:

- [Trunk Check](./Check.md): 언어 서버가 내장되어 있고 기존 이슈를 감지하는 플러그형 수퍼린터
- [Trunk Tools](./Tools.md): 비공개 런타임 및 CLI 도구 관리자
- [Trunk Actions](./Actions.md): 로컬 워크플로 자동화 및 깃훅 관리자
- [Trunk Merge](./GitHub%20Integration.md): 깃허브에서 코드를 더 안전하고 쉽게 병합할 수 있는 병합 대기열

[Trunk Check](./Check.md) 및 [Trunk Actions](./Actions.md)은 호스팅 서비스에 의존하거나 Trunk 계정이 없어도 로컬에서 완전히 사용할 수 있습니다.

## 리포지토리에서 Trunk Init하기

[Trunk Check](./Check.md), [Trunk Merge](./GitHub%20Integration.md), [Trunk Actions](./Actions.md) 또는 위의 모든 것을 사용하려는 경우 첫 번째 단계는 git 리포지토리에서 Trunk를 초기화해야 합니다:

```sh
trunk init
```

참고: 보안을 강화하려면 `trunk init` 대신 `trunk init --lock`을 선택적으로 실행하여 Trunk Config 파일에 Trunk CLI의 sha256을 추가할 수 있습니다. 그러면 Trunk Launcher가 `trunk` 바이너리를 다운로드할 때 이 파일을 사용합니다.

`init`은 리포지토리의 파일을 스캔하여 리포지토리에 맞는 `.trunk/trunk.yaml` 구성 파일을 생성합니다(`.shellcheckrc`와 같은 린터별 구성 파일도 생성할 수 있음). 이 검사는 사용하는 모든 특정 언어와 기술을 식별하고 실행할 올바른 린터/포맷터 세트를 자동으로 구성합니다.

[Trunk Merge](./GitHub%20Integration.md)만 사용하려는 경우, 린터 설정을 무시하거나 활성화된 린터를 `.trunk/trunk.yaml`에서 제거할 수도 있습니다. `trunk 로그인`을 통해 `trunk 병합`(./GitHub%20Integration.md)을 사용하려면 추가적으로 로그인해야 합니다.

## 다음 단계

[Trunk Merge](./GitHub%20Integration.md), [Trunk Check](./Check.md), [Trunk Actions](./Actions.md) 문서를 확인하세요.
