---
title: NEST - socket
feed: show
---

## WebSocket
- 네스트에서 socket을 사용하기 위해서는 gate를 사용한다.
	- @WebSocketGateway라는 데코레이터를 이용해 만들 수 있다.
	- Gateway는 Value, Service, Provider 앞단에서 웹소켓을 처리하게 된다.
- [공식 문서](https://docs.nestjs.com/websockets/gateways) 보고 설치하면 된다.
- socket.io도 Nest의 방법으로 모듈화하여 사용하게 된다.
- 결국 socket.io와 같은 구조로 구성된다.
	- 익스프레스와 같은 기반을 사용하고, 모듈화 되어있다는 차이만 있을 뿐 근본은 동일하다.
	- 익스프레스와 코드를 비교하면서 익숙해지면 좋다.