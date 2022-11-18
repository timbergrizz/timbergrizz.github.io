
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