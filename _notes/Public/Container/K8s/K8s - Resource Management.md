---
title: K8s - Resource Management
feed: hide
---

## Resource Management
- pod에 당연히 자원을 할당할 수 있다.
	- CPU / Memory 할당 되고, 네트워크는 아직 안될거다.
	- ms라는 단위를 사용해 CPU를 할당하고, 1000ms가 1CPU로 볼 수 있다.
- 쿠버네티스에서 한 자릿수대가 나올 수 있다.
	- CPU를 한개도 안 쓰는 것이고, 효율성이 엄청나게 올라게 된다.
- CPU에는 request와 limit이 있다.
	- request는 처음 할당되는 자원이고, limit은 최대 사용할 수 있는 자원이다.
	- 메모리도 limit까지 늘어날 수 있다.
- kubectl top 등의 명령을 이용해 자원의 사용을 모니터링을 할 수 있다.
	- namespace에 메모리와 cpu를 할당할 수 있다.
- request와 limit을 지정할 수 있다.
	- Pod 뜰 때 이거 안주는데, 안주면 기본값으로 들어간다.

- Overcommitted state
	- pod를 띄웠는데 limit의 총합 값이 서버의 총 물리적 메모리가 큰 경우이다.
		- 뜰때는 request가 커서 되는데, limit 찍어서 안되면 overcommited라 한다.
			- 이를 쿠베는 request만큼 cpu를 내리고, 램 부족하면 pod 리스타트 갈긴다.
			- 아니면 cpu 쓰로틀링 건다.
	- request와 limit 값 같이 줘서 이러한 상황을 발생하지 않도록 하는 것이 좋다.
- 개발과 운영 과정은 다르고, 이러한 점에서 할당하는 것이 굉장히 중요하다.
	- vertical autoscaling이 있는데, 필요한 만큼 늘리게 된다.
	- manual로 두면 최대까지 먹여볼 수 있고, 그걸로 production 가기 전에 부하 테스트 해서 성능을 설정할 수 있다.
		- 한계상황까지 부하를 주어야 테스트를 진행할 수 있는 것이다.
- [locust]([https://locust.io/](https://locust.io/))라는 부하 테스트 툴 사용할 수 있다.
	- 이거 러닝커브가 낮고 쿠베 내에서 실행할 수 있어서 좋다.
	- [부하 테스트 방법론]([https://bcho.tistory.com/787?category=75945](https://bcho.tistory.com/787?category=75945)) 여기서 확인해봐라.
	- [이거 보고]([https://bcho.tistory.com/1371](https://bcho.tistory.com/1371)) 테스트 진행할 수 있다.
- 사실은 키워드만 알면 된다.
	- 이 강의에서 집중해야 하는 것은 쿠버네티스 100% 이해하는 것이 아닌, 쿠베의 키워드에서 필요한 단어들을 이해하는 것이다.
	- 팁 같은걸 많이 노트해두면 좋다.
		- 쿠베 자체는 책 보면 다 된다.
		- 강의 learning path랑 팁을 참조하는 것이 좋다.

