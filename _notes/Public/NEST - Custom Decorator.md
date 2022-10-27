---
title: NEST - Custom Decorator
feed: show
---

- Req, Res, Token 등 종속성이 생길 수 있는 데코레이터 요소의 경우 빼주는 것이 좋다.
	- 이러한 부분을 제거하기 위해 custom decorator를 정의하여 사용하도록 할 수 있다.
	- 동시에, request의 타입을 추론할 수 있도록 설계할 수 있다.
- 다음과 같이, 미들웨어처럼 사용될 수 있는 데코레이터를 새롭게 정의할 수 있다.

```ts
import { createParamDecorator, ExecutionContext } from '@nestjs/common'

export const User = createParamDecorator(
    (data : unknown, ctx: ExecutionContext) => {
        const request = ctx.switchToHttp().getRequest();
        return request.user;
    })
```
- 컨트롤러 단에서 req, res 사용하지 않도록 하는 것이 중요하다.
	- 최대한 이렇게 데코레이터를 정의해서 값을 가져오도록 하는 것이 좋다.
		- 쓰면 테스트하거나 프레임워크 갈아 끼울때 골치 아플 수 있다.
		- 그떼미디 일일히 찾아서 수정해주면 신나는거다.
			- 이것도 일종의 중복을 수정하는 거라 할 수 있다.

- 코드에 실행 컨텍스트가 존재한다.
	- Nest에서 여러가지 서버를 동시에 열 수 있고, 하나의 실행 컨텍스트에서 모두 관리할 수 있는 것이다.
	- 가져올 때 모두 처리할 수 있도록 하는 것이다.