---
title: K8s - GCP Deploying
feed: hide
---
## GCP 설정
- 구글은 무려 300달러 크레딧을 묻지도 따지지도 않고 준다.
- 근데 ssh 세팅하는게 좀 짜친다.
	- 즈그 클라이언트를 안깔면 접근이 안되도록 해놨다.
- 여튼 compute engine 데비안으로 하나 띄웠다. ![[Screenshot 2022-10-06 at 22.52.18.png]]
	- 브라우저에서 바로 쉘을 띄울수 있다.

## GCP에 Minikube 올려보기
- 우선 도커를 까셔야한다. 드라이버로 도커를 쓴다.
	- [이 링크](https://docs.docker.com/engine/install/debian/ )보시고 열심히 까시면 된다.
	- OS 아무 생각 없이 했더니 데비안으로 깔려서 맞춰서 데비안으로 설치했다.
- 그다음 다음과 같이 minikube를 설치할 수 있다.
	```sh
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```
- 이때 docker를 sudo 없이 쓰기 위해서 다음과 같이 도커 그룹 만들어주어야 한다.
	```sh
	sudo usermod -aG docker $USER && newgrp docker
	```
- 그리고 minikube start를 쳐주면 알아서 필요한걸 세팅해준다.
	```sh
	minikube start
	```


## yaml 파일 이용해 Deployment 생성하기

- Pod는 Replica 단위로 복제되고, Deployment를 통해 Replica를 배포할 수 있다.
- hello minikube의 Deployment는 다음과 같이 구성된다.
	```sh
	kubectl create deployment hello-node --image=registry.k8s.io/echoserver:1.4
	```
	
- 이를 바탕으로 다음과 같이 yaml 파일을 작성한다.
	```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-node
  labels:
    app: hello-node
spec:
  replicas: 1
  selector:
    matchLabels:
      app: hello-node
  template:
    metadata:
      labels:
        app: hello-node
    spec:
      containers:
        - name: hello-node
          image: registry.k8s.io/echoserver:1.4
          ports:
            - containerPort: 80
```
- 그러면 다음과 같이 deployment와 pod이 생성된 것을 확인할 수 있다.![[Screenshot 2022-10-06 at 23.22.21.png]]![[Screenshot 2022-10-06 at 23.22.39.png]]
- 다음과 같이 발생한 이벤트들도 확인할 수 있다.![[Screenshot 2022-10-06 at 23.24.22.png]]
- kubectl 설정도 다음과 같이 확인할 수 있다. ![[Screenshot 2022-10-06 at 23.24.48.png]]

## Service 생성하기
- deployment를 외부로 expose시킬 수 있어야 한다.
	- ip와 port를 명시해야 하고, 다음과 같이 적용한다.
- 다음과 같이 Service에 대한 yaml파일을 생성할 수 있다.
	```yaml
apiVersion: v1
kind: Service
metadata:
  name: hello-node
  namespace: default
  labels:
    app: hello-node
spec:
  selector:
    app: hello
  ports:
    - protocol: TCP
      port: 8080
      nodePort: 30376
  type: NodePort
```
- 다음과 같이 expose가 실행된 것을 확인할 수 있다.![[Screenshot 2022-10-06 at 23.34.16.png]]
- ``` kubectl get service ``` 를 통해 정상적으로 실행되었는지 확인할 수 있다.![[Screenshot 2022-10-06 at 23.36.40.png]]


## Using Minikube to Create a Cluster
### Kubernetes Clusters
- 쿠버스터는 여러개의 컴퓨터를 묶어 하나의 유닛으로 사용한다.
	- 각각의 컨테이너 어플리케이션을 따로 머신에 할당할 필요 없이 클러스터로 만들어 할당할 수 잇따.
- 이러한 배포 모델을 위해, 어플리케이션은 새로운 방법으로 패키징되어야 한다.
	- 컨테이너라이징된 어플리케이션은 기존 deployment 형태보다 더 유연하고 가용성이 높다.
- 쿠베는 어플리케이션의 배포와 컨테이너의 스케줄링을 더 효율적인 방법으로 수행한다.
- 쿠베 클러스터는 2가지 리소스로 나뉜다.
	- Control Plane은 클러스터를 관리한다.
	- Node는 어플리케이션을 작동시키는 Worker가 된다.
![](https://d33wubrfki0l68.cloudfront.net/283cc20bb49089cb2ca54d51b4ac27720c1a7902/34424/docs/tutorials/kubernetes-basics/public/images/module_01_cluster.svg)

- Control Plane 클러스터를 관리한다.
	- 클러스터의 모든 활동을 관리한다.
		- 어플리케이션 스케줄링, 어플리케이션 상태 관리, 스케일링, 업데이트 관리등을 담당한다.
- Node는 워커 머신으로 작동하는 VM / 물리적 컴퓨팅 자원이다.
	- 각 노드는 노드를 관리하고 Control Plane과 통신하는 kubelet을 가지고 있다.
	- Node는 컨테이너 동작을 handling하는 Docker같은 컨테이너를 포함하고 있다.

- 어플리케이션을 쿠버네티스에 deploy하면 다음과 같은 과정을 거친다.
	- Control Plane에 app container를 시작하라고 명령한다.
	- Control Plane은 Cluster의 Node들에 컨테이너를 스케줄링한다.
	- Node는 Kubernetes API를 이용해 Control Plane과 연결된다.
	- 엔드 유저도 cluster에 접근하기 위해 Kube API를 사용할 수 있다.
- 쿠버네티스 클러스터는 물리적 / VM 환경에 deploy될 수 있다.
- 쿠버네티스 개발을 시작할때 Minikube 써도 된다.
	- Minikube 쓰면 로컬 머신에 VM 만들고, 하나의 노드를 포함하는 심플한 클러스터를 만든다.
- Minikube CLI는 클러스터에서 작동하는 기본적인 bootstrapping 동작들을 지원한다.
	- start, stop, status, delete
## Using kubectl to Create a Deployment
- 쿠버네티스 클러스터 올려봤으면, 컨테이너로 만든 앱을 올려볼 수 있다.
	- 이를 위해서는 deployment configuration을 해보면 좋다.
- Deployment는 쿠버네티스가 어플리케이션의 인스턴스를 어떻게 조정할 것인지를 설정한다.
- Deployment를 만들면, Control Plane이 어플리케이션 인스턴스를 각 노드에 동작시키도록 스케줄링한다.
- 어플리케이션 인스턴스가 만들어지면, Deployment 컨트롤러가 인스턴스를 모니터링한다.
	- Node에서 인스턴스가 내려가거나 삭제되면, 컨트롤러가 클러스터의 다른 노드로 자동으로 복구한다.
	- 클러스터에 대한 문제 / 점검에 대한 자가 복구 매커니즘을 지닌다.
- ㅖ전의 아키텍처는 이러한 자가 복구 능력이 부족했지만, 쿠버네티스는 합니다.![](https://d33wubrfki0l68.cloudfront.net/8700a7f5f0008913aa6c25a1b26c08461e4947c7/cfc2c/docs/tutorials/kubernetes-basics/public/images/module_02_first_app.svg)
- Deployment는 Kubectl 이용해서 할 수 있다.
	- Kubectl은 ubernetes API로 클러스터와 소통한다.
- Deployment를 생성하면, 컨테이너 이미지와 레플리카의 개수를 정의해야 한다.
	- 이러한 정보를 deployment 업데이트하면서 반영할 수 있다.
	- NGINX에 올라간 튜토리얼을 따라해보면서 한번 해보자.

## Pods & Nodes
### Pods
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
### Nodes
- Pod는 항상 Node 위에서 실행된다.
- Node는 쿠버네티스의 worker machine으로, VM이나 물리적 컴퓨팅 환경에서 동작한다.
- Node는 여러개의 Pod을 가질 수 있으며, Control Plane은 클러스터 내부의 node들에 대해 pod에 대한 스케줄링을 수행한다.
	- Control Plane의 자동 스케줄링은 각 노드의 가용 리소스를 찾아서 활용한다.
- 모든 쿠버네티스 Node는 다음으로 구성된다.
	- Kubelet
		- 쿠버네티스 control plane과 Node를 연결한다.
		- Pod과 container를 매니징한다.
	- 컨테이너 런타임
		- 컨테이너 이미지를 작동시키고, 컨테이너의 압축을 해제해서 어플리케이션을 작동시킨다.

### Troubleshooting with kubectl
- kubectl의 가장 일반적인 operation이 다음과 같이 존재한다.
	- kubectl get : 리소스를 리스팅한다.
	- kubectl describe : 리소스에 대한 자세한 정보를 가져온다.
	- kubectl logs : pod 내부 컨테이너의 로그들을 출력한다
	- kubectl exec : pod 내부 컨테이너에 명령을 실행한다.

## Using a Service to Expose Your App
### Overview of Kubernetes Services
- 쿠버네티스의 Pod는 라이프사이클을 가지고 있다.
	- 워커 노드 죽으면 안에 있던 Pod 같이 죽는다.
	- ReplicaSet이 클러스터에서 새로운 Pod 생성하여 어플리케이션 작동하도록 할 수 있다.
		- 죽어서 다른 걸로 바꾸거나 해도 크게 문제되지 않는다.
- 쿠버네티스의 각 Pod는 unique한 ip 주소를 갖고 있다.
	- 같은 노드에 들어있는 Pod 또한 그러하다.
- 쿠버네티스의 서비스는 Pod의 논리적인 집합과 이를 접근하는 정책에 대한 추상화이다.
	- 서비스는 의존적인 Pod를 루즈한 couplingㅡㅇ로 정의할 수 있다.
- 서비스는 YAML이나 JSON등의 문서로 표현될 수 있다.
- Pod 집합은 서비스로 LabelSelector를 통해 타겟팅 된다.
- 각 Pod가 Unique한 ip 주소를 갖고 있지만, 클러스터 외부에서 서비스 없이 이를 사용할 수 없다.
	- 서비스는 어플리케이션이 트래픽을 수용할 수 있도록 한다.
- 서비스는 ServiceSpec에 type을 정의하여 여러가지 방법으로 노출 시킬 수 있다.
	- ClusterIP
		- 클러스터 내부의 IP를 노출시킨다. 클러스터 내부에서만 접근이 가능하도록 한다3.
	- NodePort
		- 서비스를 선택된 노드와 같은 포트에 있는 것에만 expose한다.
		- ClusterIP의 수퍼셋으로, 서비스를 클러스터 외부에서 접속할 수 있도록 한다.
	- LoadBalancer
		- 현재 클라우드에 외부 로드 밸런서를 만들어 고정된 외부 IP를 서비스에 부여한다.
		- NodePort의 슈퍼셋이다.
	- ExternalName
		- CNAME 레코드와 값을 통해 서비스의 컨텐츠를 externalName 필드로 맵핑한다.

- 서비스의 유즈 케이스에서 selector를 정의하지 않는 경우가 있다.
	- 이런 경우 대응되는 Endpoint 객체를 만들지 않는다.
	- 이렇게 해서 유저가 직접 서비스로 가는 endpoint를 정의할 수 있게 된다.
	- 또한 ExternalName 타입을 strict하게 사용하면 셀렉털르 사용하지 않는다.

### Services and Labels
- 서비스는 Pod들의 집합에서 트래픽을 연결한다.
- 서비스는 쿠베 내부에서 어플리케이션에 영향을 주지 않고 Pod이 죽고 복제되는 상황을 추상화한다.
	- 의존적인 Pod를 발견하고 라우팅하는 것은 쿠버네티스 서비스에 의해 이루어진다.
- 서비스는 label과 selector를 이용해 Pod을 찾는다.
	- Label은 키/값 페어로 연결되어 있는 객체이며, 여러가지 용도로 사용될 수 있다.
		- 개발 / 테스트 / 프로덕션을 위한 객체 디자인
		- 버전 태그
		- 객체 분류
- 