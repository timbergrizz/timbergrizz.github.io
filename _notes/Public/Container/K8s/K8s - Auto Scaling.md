---
title: K8s - Auto Scaling
feed: hide
---
## Auto Scaling
- 쿠베 오토스케일러는 3개가 동작한다.
	1. node
		- 노드의 개수를 조정한다
	2. pod - vertica
		- pod의 개수를 늘린다
	3. pod - horizontal
		- 실제 request의 값을 늘려, 자원을 더 많이 할당하도록 한다.

### Node Pool
- 노드는 다른 하드웨어로 묶을 수 있다.
	- auto scaling은 노드 풀의 단위로 묶는다
		- 모자란 단위로 증가하게 되는 것이다.
	- 다른 클라우드와 다르게 구글은 multi-regional pool로 생성할 수 있다.
	- 하나의 쿠버네티스 풀을 만들고, 존만 분리해주는 방식으로 배포할 수 있는 것이다.
- 노드 스케일 아웃이 발생하는건 언제인가?
	- pod 생성하려는데 node 자원 모자라서 생성 안되고 pending이 발생할 때 문제가 된다.
- auto-scaling은 주기적으로 node pool이 놀고 있으면 내린다.
	- 돌던거는 내리고 다른 노드에 다시 올린다.
		- 일반적으로 stateless 프로그램은 문제가 없는데, db를 이렇게 쓰는건 좋지 않다.
		- 노드풀을 나누어서 scale-out이 되는 stateless 풀과 안되는 stateful 풀을 사용하는 것이 좋다.
	- stateful 풀은 io / cpu intensive한 경우가 많아 성능을 많이 할당해야 하고, stateless는 싸게 막는 경우가 좋다.

### Horizontal Pod Autoscaler
- auto scaler 만들고 min / max 정해서 pod 늘리도록 하는 것이다.
	- 이게 사실 제일 무식한 방법이다.
	- idleThread < 5이면 쓰레드가 거의 다 돌고 있는거다.
		- 스레드 수를 바탕으로 스케일링을 할 수 있으면 최선인데, 복잡한다.
	- message queue에 worker 쓰는데, 이거 찼다는 소리는 worker가 소화가 안된다는 뜻으로 이걸로 확인할 수 있다.
		- [keda](https://keda.sh/docs/2.8/scalers/redis-lists/](https://keda.sh/docs/2.8/scalers/redis-lists/) 등을 사용해 worker node의 부하를 확인하고, 이를 해결하도록 할 수 있다.
		- 스케일할 수 있는 back worker node를 만들 수 있따.

### Vertical Pod Autoscaler
- Pod의 수와 메모리를 조정한다.
	- Auto mod와 manual mod가 있다.
		- auto는 필요할때마다 올리는데, 이게 자동으로 껐다 켠다.
	- manual은 필요한 양을 확인하고, 스케일업 안하고 이걸 보여주기만 한다.
		- 이걸로 부하 테스트하고, 이를 통한 적절한 cpu 양을 확인해서 이를 통해 pod를 튜닝하고, hpa만 작동시키면 된다.
	- vpa / hpa 두개 같이 쓰는건 별로다.

- 비용 측면에서, 같은 zone에 필요한 것은 같은 zone으로 배치하는 등으로 처리할 수 있다.
	- regional service에서는 묶어서 쓰는게 낫다.
	- 노드 양은 매니지가 되기 때문에 이건 생각할 필요 없고, hpa만 생각하면 된다.
- 보통 머신러닝 엔지니어 gpu 사주면 안하고 게임하거나 비트코인 마이닝 돌린다.
	- 제대로 된 것은 트레이닝 돌리고 있겠지?