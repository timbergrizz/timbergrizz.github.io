---
title: K8s - What is Kubernetes
feed: hide
---

-   한국은 아직도 vm 쓰는데가 있는데, 미국은 대부분 쿠버네티스를 사용한다
-   학슴의 목적은 learning path
    -   어떤 부분을 봐야할 지를 결정할 수 있어야 한다.
    -   쿠베 책 찾아보면 좋은게 많다. 어디서 써야하는지 / 어디서 쓰면 안되는지 이해하는 것이 중요한다.
        -   왜 공부하는지를 이해하면 좋을 것이다
    -   실 운영 환경 자체를 구성하는데 필요한 것을 이해하는 것이 좋다.
-   쿠버네티스를 설치하는 것은 거의 할 일이 없을 것이다.
    -   돈 주고 사는게 아니라 오픈소스 쿠베 가지고 환경 가지고 하는 사람들 외에는 설치할 일 없다.
    -   보통은 패키지성 쿠버네티스 사용하고, 이건 같이 인스톨해주기 때문에 신경쓸 필요 없다.
        -   나머지는 클라우드에서 쿠베 사용하고, 신경 쓸 필요 없다.
-   vm생성할 때, 클라우드에는 vm 종류가 어마어마하게 많다.
    -   일반적인 cpu 비용보다 싼 인스턴스가 있다.
        -   미세한 차이가 있다.
    -   그런 점에서 어떤 차이가 있고, 이걸 어떻게 다룰 수 있을지 생각해야 한다.


## 쿠버네티스

-   구글에서 보그라는 코드명으로 만들어졌다
    -   구글 클라우드가 쿠베 위에서 돈다.
        -   vm 띄우면 컨테이너 안에서 vm이 뜬다.
        -   GCP를 아키텍처 구조적으로 보면 재밌다.

### 쿠버네티스의 아키텍처

-   기본적으로 control plain 이라는 것이 있다.
    -   흔히 말하는 admin 서버들이 control plain이다.
        -   kubectl이라는 커맨드를 쓰고, UI를 사용할 수도 있는데, 외부에서 요청을 쿠베에서 받는게 API 서버가 된다.
        -   kubectl에서 create container를 만들면 데이터 플레인을 만들어야 한다.
            -   데이터 플레인에 서버가 여러개면? 이러한 서버들을 노드라 한다.
            -   노드는 VM이 될수도 있고 베어메탈이 될 수 있다.
                -   어떤 노드로 보내야 할까? Scheduler로 보내게 된다.
            -   A 서버에 배포를 하고, 컨테이너를 올리는데 이 명령어를 받는 에이전트가 Kubelet이다.
                -   Kubelet이 뜨면 컨테이너 런타임이 컨테이너를 실행시킨다.
                -   트래픽이 들어와야 하는데, 컨테이너가 여러개 돌고 있으면 어디로 보내야 할까?
                    -   트래픽을 관리해주는 컴포넌트가 kubeproxy이다.
                    -   외부에서 트래픽이 들어오면 프록시가 받아서 올바른 컨테이너로 보내게 된다.
        -   configuration 정보를 갖고 있어야 한다.
            -   ETCD라는 데이터베이스가 control plane이 있고, 여기에 저장되게 된다.
            -   hash table과 유사한 형태로 구성된다.
        -   오토 스케일링이나 크러시가 났을 때 이를 관리해주는 컴포넌트가 있다.
            -   이러한 컴포넌트를 컨트롤러라고 한다.
-   이후 Data plain이 들어간다.
    -   실제 워크로드가 여기서 돌아간다.
-   쿠버네티스는 여러개의 노드 / VM 위에서 돌아가는 분산형 아키텍처라 한다.
    -   실제 워커 노드를 돌리는 것을 data plane이라 한다.
-   커맨드를 받는 것은 마스터 노드의 api 서버가 담당한다.
    -   컨테이너를 호스팅해주는 역할을 하고 밖에서 들어오는 트래픽은 kubeproxy가 라우팅 해주는 역할을 한다.
    -   어디 서빙될지는 scheduler가 관리하고, api 서버등의 매니징한다.
    -   controller-manager 같은걸 batch job / auto scaling등의 동작을 한다.
    -   kube-scheduler등이 존재한다.
    -   kube-dns
        -   쿠베를 띄우면 노드들이 있고, 컨테이너들이 뜰거다.
            -   어떤 노드에 컨테이너가 뜨는지 모르고, ip address는 매번 바뀐다.
            -   ip로 포인팅하면 호출할 수 없다.
                -   서비스라는 개념이 생기게 된다.
                -   그렇기 때문에 DNS를 사용하게 되고, 따라서 쿠베 내장 DNS를 사용하게 된다.
    -   Pod로 직접 트래픽을 안보내고, 로드밸런서가 Pod 묶어서 트래픽을 보내게 된다.
        -   로드밸런서에 DNS를 두게 된다. 이를 서비스명이라 한다.
            -   DNS에 하면 서비스명이 부여가 된다.
        -   하나의 클러스터에 15000개의 노드를 붙일 수 있고, 한 개의 노드에서 256개의 컨테이너까지 붙일 수 있다.
            -   총 384만개의 컨테이너를 띄울 수 있다.
        -   클라우드의 자원에는 논리적 한계가 있다.
            -   하나의 아마존 서버에서 384만개를 주면 로드밸런스를 못주는 리소스 문제가 발생하게 된다.
                -   DNS가 터진다. 이거 조그마한 컨테이너로 뜨는거다.
                -   어느정도 규모 이상의 컨테이너는 설계 구조가 달라지게 된다.
                    -   벤더마다 클라우드 DNS가 따로 존재하고, 이를 사용하도록 config를 바꿔주어야 한다.
                -   컨테이너 노드 하나당 256개를 띄울 수 있고, 256개의 ip range를 물고 들어간다.
                    -   50개를 띄운다고 해도 256개를 먹는다.
                    -   조그마하게 만드는 일반적인 클러스터도 디자인이 바뀌어야 한다.
                        -   1000단위 노드는 그냥 놔두지도 않고, 비싼 엔지니어를 고용하게 된다.
                        -   시스템 몰리기 시작하면 아키텍처 컨설팅을 하고, 잠재적인 문제를 분석하는 일을 하는 사람들이 있다. 내가 한다.
                    -   랜덤으로 ip가 지정되고, 연속성의 Ip가 지정되기 어렵다. ip range를 지정하기도 어렵다.
                        -   자기가 가지고 있는 ip를 클라우드에 등록해서 사용하는 방법이 있고, 이를 설계한다.
                        -   스타트업 들어가도 맨땅에 대가리 받지 말고, 담당 엔지니어 있다.
                            -   optimize 해야합니다. 이런 걸 얘기해주는 건 프로젝트 할 때 먼저 가서 얘기하라는 뜻이다.
                    -   CDN같은게 이런 케이스에 많이 들어간다.
                        -   멀티 CDN 계약 전략을 하는 등으로 비용을 절약할 수 있다.
                        -   여러개의 워크로드를 전환할 수 있도록 하고, 재계약때 갈아치우는 방법을 사용하면 좋다.
                            -   벤더 락인 걸리면 안된다. 머리 쓰면 좋은 방법이 많다.
                            -   서버리스를 람다만 쓰라는 법은 없고, 여러가지 서비스를 사용할 수 있다.
                -   여러개의 클라우드 서비스에 쿠베가 있을때 ISTIO를 띄워서 가상화 네트워크를 만들고, 엔드포인트로 이걸 로드밸런싱을 하면, 클라우드별로 분류할 수 있는 것이다.
                    -   내 맘대로 트래픽을 보낼 수 있는 것이다.
                    -   이정도면 상당한 고수다.
                        -   마이크로 서비스가 트래픽을 자동으로 잡는다.
                        -   트래픽 라우팅을 하고, 카나리 테스팅을 MSA를 통해 할 수 있는 것이고, 에러를 실시간으로 모니터링 할 수 있는 것이다.
            -   대시보드
                -   쿠베 대시보드 안쓴다.
                    -   UI인데, 요즘은 좀 괜찮은데 예전에는 인증 기능이 없었다.
                        -   OAuth등의 기능을 써야 하는데, 인증 제대로 작동 안되면 비트코인 채굴장 되는거다.
                -   쿠베 기술중에 binary auth 기능이 있다.
                    -   컨테이너 이미지 아무거나 써서 문제 생기는 경우 많다.
                    -   도커 이미지 아무거나 찾아 쓰면 지옥갑니다.
                        -   가이드를 해도 신입은 지좆대로 한다. 그래서 공인된 이미지만 사용하도록 강제할 수 았다.
                        -   개발은 편하게, 정책을 강하게.

#### Pod
-   쿠버네티스의 최소 배포 단위
	-   컨테이너가 아니다.
	
-   팟은 한개 이상의 컨테이너로 구성된다.
	-   하나씩 컨테이너 배포 안하고 왜 이렇게 하지?
		-   어플리케이션을 보통 배포하면 어플리케이션 하나만 나이스하게 배포하지 않는다.
		-   node.js 어플 배포하려면 기본적으로 로깅 툴 / 모니터링 에이전트 붙는다.
			-   이걸 하나로 묶으면, 패키지 하나씩 바꿀때마다 신나진다.
			-   편차가 심하다. 운영하는 입장에서 통일해야 한다
				-   이런 경우 이런것들을 잘라서 배포하면 된다.
				-   CI / CD는 컨테이너 매번 새로 배포해야 한다.
			-   모니터링 / 로깅 툴을 분리하여 컨테이너로 설계하면 어떨까?
				-   운영 팀이 모니터링 / 로깅을 책임, 어플리케이션은 개발 팀이 책임지는 방식으로 작동한다.
			-   이걸 사이드카 패턴이라 한다.
				-   굉장히 널리 쓰이는 패턴이다.
				-   프록시 등 인젝션할 때 자주 사용된다.
				
-   ip address와 disc를 공유한다.
-   쿠베의 리소스들의 포드 등에는 레이블을 달 수 있다.
	-   일종의 식별자인데, 어플리케이션을 배포하고 프론트라인 / 백엔드 / 데이터베이스가 존재한다.
		-   프론트엔드의 CP만 보고싶을 때, 태그만 가지고 배포할 수 있을 것이다.
		-   프론트엔드 서버에만 롤을 주는 등의 선택을 하도록 할 수 있다.
	-   방화벽 규칙 등에 활용할 수 있다.
		-   내부 트래픽 통제를 위해서 태그를 사용할 수 있다.
			-   ip를 강제할 수 없기 때문에 태그를 지정한다.
	-   태그는 한 개 이상을 작성한다.
		-   이런 식으로 레이블을 사용하게 된다.
-   어플리케이션 돌릴때 컨테이너를 여러개 띄운다.
    -   템플릿 식으로 pod를 여러개 띄우는 것이 replication controller이다.
        -   api 서버 있고 이거 관리하는 컨트롤러이다. 그 중 첫번째가 지금 애기하는 replication controller이다.
            -   replica를 몇개 띄울지 결정해준다.
            -   레이블에 매칭이 될 때 까지 replication이 띄운다.
        -   템플릿에 의해 배포되지 않았아도 레이블 수만 맞으면 된다.
        -   replication controller에 의해 포드를 계속 띄우게 된다.
            -   다른 사람이 추가로 띄워도 그것도 띄운걸로 치다.
        -   어플리케이션 내용이 어떤지 상관 없이 레이블이 만든대로 로드밸런싱을 한다.
            -   카나리 테스트 등을 이러한 방식으로 적용할 수 있다.
        -   프론트엔드 입장에선 앞의 레이블만 확인하고, 따라서 이에 따라 달라지게 되는 것이다.
    -   replica set
        -   replication controller와 동일한데, 레이블을 선택하는 방식이 다르다.
        -   replica set은 집합을 사용한다.
            -   레이블의 셋을 묶어서 집합으로 띄우게 된다
            -   canary deployment등의 고급 기능을 필요로 한다.


#### Deployment
-   버전 2를 업그레이드하고 싶으면, 새로운 컨테이너를 띄우고 로드밸런스로 스위칭 할 수 있다.
	-   이렇게 작동하면 리소스가 6개 필요하고 부족할 수 있다.
-   기존거를 하나씩 지우고 새로 띄우는 방식으로 업데이트 할 수 있다.
	-   롤링 업데이트라 한다.
	-   deployment라는 것은 기능을 많이 필요로 한다.
-   Service
	-   Selector에 따라서 로드밸런싱을 진행한다.
-   여러가지 pod가 뜨면 하나로 묶어서 내는 것을 서비스라고 한다.	
	-   이정도면 api server는 만들 수 있다.

- Deployment를 만들면, 쿠버네티스는 어플리케이션 인스턴스를 생성하기 위해 Pod을 만든다.
	- Pod은 쿠버네티스에서 하나 이상의 컨테이너와 컨테이너간의 자원을 공유하는 하나의 단위이다.
- Pod은 다음과 같은 자원을 공유한다.
	- 볼륨과 같은 공유 스토리지
	- unique한 클러스터의 IP
	- 각 컨테이너를 작동시키는 방법
		- 컨테이너의 이미지 버전이나 사용하는 퍼트 번호
- Pod 모델은 어플리케이션 관점에서 논리적인 호스트이고, 관계가 큰 서로 다른 어플리케이션을 묶을 때 사용할 수 있다.
- 같은 Pod에 속한 컨테이너들은 IP 주소와 Port 공간을 공유한다.
	- 항상 같은 곳에 위치하고 같이 스케줄된다.
	- 같은 node의 동일한 context에서 실행된다.
![](https://d33wubrfki0l68.cloudfront.net/fe03f68d8ede9815184852ca2a4fd30325e5d15a/98064/docs/tutorials/kubernetes-basics/public/images/module_03_pods.svg)