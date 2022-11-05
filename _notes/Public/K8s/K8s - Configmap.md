---
title: K8s - Configmap
feed: hide
---
## Configmap
- 우리가 환경 설정 파일 빼는데, 이렇게 환경 설정 파일 빼는걸 configmap이라 한다.
	- 운영 환경과 개발 환경 뺄 수 있고, 이를 환경 변수로 빼는걸 configmap이라 한다.
- deployment에서 key가 language인 것의 값을 ㅂ다아 넣어주는 형식으로 만들 수 있다.
	- 코드 내에서 환경변수 내에 있는 값을 읽어서 사용하는 방식으로 사용하게 된다.
	- 환경 변수가 여러개 존재할 수 있고, 파일 자체를 configmap으로 설정할 수 있다.
		- 이 파일은 환경 변수로 넘기는게 아니라, 마운트를 시켜 처리해버릴 수 있다.
		- 그럼 configmap의 파일이 파일 시스템에 묶어서 올라간다.
- 파일도 할 수 있고, 디렉토리도 올릴 수 있다.
	- 반드시 작성해야 하는 것 중 하나이다.
- ingress 쓸때 서비스에서 clusterIP나 LoadBalance 쓴다.
	- External Loadbalancer 쓰지 마라.
		- 이거 밖으로 빼는 방법은 좋지 않다.
		- internal type을 사용하는 것이 좋다.
- ingress로 로드밸런싱을 할 경우 굳이 load balance type으로 열 필요가 없다.

### Secret
- Configmap과 동일한데, configmap은 모든 값을 조회할 수 있다.
- 실제로 클라우드에서는 키 매니지먼트 서비스가 존재한다.
	- HSM이라는 모듈이 있는데, 보안키를 저장할 수 있다.
	- 이걸 사용하려면 접속하려는 키가 필요하다.
	- 쿠버네티스에서는 키가 KMS에 저장한다.
- config map은 etcd에 저장되는데, 이 노드가 탈취되면 다 털리는거다.
	- KMS는 쿠베 밖에 있어서 못턴다.
	- 그래서 AWS [EKS Secret]([https://docs.aws.amazon.com/secretsmanager/latest/userguide/security-encryption.html](https://docs.aws.amazon.com/secretsmanager/latest/userguide/security-encryption.html)) 같은걸 써서 중요한 키 등을 저장한다.

### Volume
- 볼륨은 디스크이다.
	- 네트워크 디스크 등이 있는데, 한가지 타임은 emptyDir이다.
		- Pod 띄우면 Pod 안에 로컬 디스크 공간을 볼륨으로 정의하는 것이다.
		- 컨테이너를 Pod에 2개 이상 띄울 수 있는데, 양쪽에 다 물리기 위해서 쓰는 것이다.
	- hostPath
		- 노드에 있는 디스크 공간을 공유한다.
			- Pod 밖의 데이터를 공유할 수 있도록 한다.
			- deployment로 컨테이너 다시 살아나는 경우 다른 노드의 디렉토리이다.
			- 일반적으로 잘 사용하지 않는다.
	- PersistenceVolume
		- 외부 스토리지를 물리는 방식이다.
		- 가장 일반적으로 사용한다.
		- 클라우드 컴퓨팅 환경에서 외부에 생성한 디스크는 원래 보이지 않는데, 이를 접근하도록 한다.
		- 하드웨어 디스크 등록했을 때, 이를 Pod로 연결할 수 있도록 하는 것을 PersistenceVolume이다.
			- 볼륨 모드를 보아야 한다.
		- 파일 시스템은 일반적으로 생각하는 그거다.
		- rawblock mode라는 것이 있따.
			- sfs같은 게 있는데, 이런걸 raw disk를 물려야 한다.
		- Reclaim Policy
			- 사용 끝나면 어떻게 할 것인가?
				- Delete : 디스크 지워버린다.
				- Retain : 디스크 남겨둔다.
				- Recycle : 디스크 지우지 않고 내용만 다 지운다.
					- 이러한 정책을 사용하는데는 크게 두가지 이유가 있다.
						- 디스크 가격 정책때문에 그럴 수 있다.
							- 최저 사용 시간등이 있을 수 있다.
						- 클라우드 자원은 무제한이 아니고, 프로비저닝 과정에서 경합이 발생하는 경우가 발생한다.
							- 이를 선점하기 위해서 사용할 수 있다.
		- Read-write policy
			- ReadWriteOnce : 한 파일만 읽고 쓰기만 가능하다.
			- ReadWriteMany : 여러번 읽고 쓰기가 가능하다.
		- 디스크는 상태가 있고, attach가 되면 bound가 된다.
			- 사용이 끝나면 release가 된다. 이때는 아직 사용 불가능한 상태고, 사용 가능한 상태가 되면 available로 바뀐다.
		- 디스크를 매번 직접 생성할 수 없다.
			- 이를 클라우드 설정에 명시하여 동적으로 프로비저닝할 수 있다.
			- 100메가짜리 디스크를 testtype으로 선언하는 것이다.
				- PersistentVolumeClaim 등으로 선언할 수 있다.
