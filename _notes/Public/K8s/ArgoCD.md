---
title: ArgoCD
feed: hide
---
# ArgoCD

## GitOps
- 원본 출처 : [ArgoCD란? ArgoCD 개요 및 설치](https://nayoungs.tistory.com/entry/ArgoCD란-ArgoCD-개요-및-설치)
- DevOps의 전략 중 하나이다.
	- 배포와 운영에 관련된 모든 요소들을 Git에서 관리한다.
- GitOps는 Git Pull 요청을 통해 인프라 프로비저닝 및 배포를 자동으로 관리한다.
	- 레포지토리에는 전체 시스템 상태가 포함되어 시스템 상태의 변화 추이를 확인, 감사할 수 있다.

### GitOps의 원칙
- 모든 시스템은 명시적으로 선언되어야 한다.
- 시스템의 상태는 Git의 버전을 따라간다.
- 승인된 변화는 자동으로 시스템에 적용된다.
- 배포에 실패하면 이를 사용자에게 경고해야 한다.

## ArgoCD
- GitOps 방식으로 관리되는 Manifest 파일의 변경 사항을 감시하며, 현재 배포된 환경의 상태와 Github Manifest 파일에 정의된 상태를 동일하게 유지한다.
	- 쿠버네티스를 위한 CD 툴이라고 할 수 있다..
	- 쿠버네티스의 구성 요소를 관리 / 배포하기 위해 Manifest를 구성하여 실행해야 한다.
		- 이러한 파일들은 지속적인 변경이 있어, 관리가 필요하다.
		- 이를 Git으로 관리하는 방법이 GitOps이고, 이를 실현시켜 쿠베에 배포까지 하는 툴이 ArgoCD이다.