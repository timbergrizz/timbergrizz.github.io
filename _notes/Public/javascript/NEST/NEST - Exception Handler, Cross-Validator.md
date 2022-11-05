---
title: NEST - Exception Handler, Cross-Validator
feed: hide
---

- 일반적으로 오류 발생했을 때 exception으로 핸들링 처리를 할 수 있다.
	- 근데 이렇게 기본적인 처리만 하면 response에 에러가 담기지 않고, 소스에서 이걸 보내줄 수 있어야 한다.
- Nest에는 HttpException이 존재하고, 이를 통해 Exception을 전송할 수 있다.
	- HTTP exception들에는 이름 있어서 이 예외들에 맞춰서 적으면 에러 코드 없이 처리하도록 할 수 있다.
- 원하는 데이터가 아니면 예외 처리 던지도록 하면 되는데, 자동으로 검증해주면 두배로 좋지 않을까?
	- DTO를 통해 검증을 할 수 있다.
	- class-validator를 이용해 검증할 수 있다
		- 직접 검사하는 로직 붙이지 말고 DTO 위에 검증하는 로직 붙여서 사용하는 편이 990423565배 낫다.
	- Filtering 로직을 통해 에러를 직접 포맷팅해서 전송할 수 있다.
- [Interceptor](obsidian://open?vault=markdown_git&file=%EC%86%8C%ED%94%84%ED%8A%B8%EC%9B%A8%EC%96%B4%20%EB%A7%88%EC%97%90%EC%8A%A4%ED%8A%B8%EB%A1%9C%2FNestjs%2FloggerMiddleware%2C%20implements) 로도 가능한데, 이건 다른 용도로 사용해야 한다.
	- 에러처리 용도로는 Exception Filter, class validator를 사용하는 것이 좋다.
	- Interceptor는 컨트롤러 앞 - 뒤에서 실행되고, Exception filter는 컨트롤러 뒤에서 실행된다.
		- 라이프사이클을 이해하고 사용하면 좋다.
		- 실행 순서는 [공식 문서](https://docs.nestjs.com/faq/request-lifecycle)에서 확인하도록 하자.
			- 실행 여부에 따라 에러의 위치를 이해할 수 있다.
- NestJs는 모두 모듈 기반으로 동작한다.
	- 그래서 다 따로 패키지가 존재한다. passport도 동일하게 따로 존재한다.
	- 그냥 passport 써도 되긴 한데 모듈로 감싼거 쓰는게 낫다.


