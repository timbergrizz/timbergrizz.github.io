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
