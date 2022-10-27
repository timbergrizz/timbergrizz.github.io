---
title: NEST - NestJS 세팅
feed: show
---

# NestJS 세팅하기
```toc
```
## 기본 설정
- 우선 nest.js cli를 설치해야 한다.
	- npx를 이용해 설치할 수도 있으나, package.json이 생성되기 전에 작동해야 하므로 그냥 global로 설치해서 처리하는게 빠를 것이다.
	- 다음과 같이 설치할 수 있다.
	```bash
	npm i -g @nestjs/cli
	```
	- 이후 다음과 같은 명령을 통해 새로운 nest 프로젝트를 시작할 수 있다.
	```bash
	nest new (프로젝트명)
	```

- 이때 패키지 매니저로 yarn과 npm중에 선택할 수 있다.
	- npm이 기본이고 yarn 기능 많이 가져오기도 해서 이거 써도 괜찮다.
- 처음 시작하면 다음과 같은 파일들이 생성된다. ![[Screenshot 2022-10-03 at 3.03.41.png]]
	- 이러한 파일들이 모두 이유가 존재하게 된다.
	- 개발상의 편의를 위해 다음과 같은 옵션을 추가하면 좋다.
		```json
		"esModuleInterop" : true
```
		- 다음 코드를 사용하면 다음과 같이 사용할 수 있다.
		```ts
		import * as React from React // 사용 전
		import React from React // 사용 후
		```

- package.json 확인하면 여러가지 기본 패키지가 있다.
	- 대부분 타입스크립트를 위해 기본적으로 필요한 모듈이다.
- rx.js는 서비스에 필요한 부분에만 사용할 수 있다.
	- Angular에서 많은 영향을 받았고, 다른 부분들이 존재한다.
- 응답 테스트등이 미리 설정되어 있다.

- package.json에 명시된 대로 run 스크립트를 실행시키면 서버가 작동하는 것을 확인할 수 있다.![[Screenshot 2022-10-03 at 3.11.11.png]]
	- 이때 확인해보면 express를 바탕으로 구성되어 있는 것을 확인할 수 있다.
		- fastify를 쓸 수 있는데, 설계적으로 우수하긴 한데 굳이 쓸 이유 없다.
			- Nest가 Express의 설계 결함을 해결해주기 때문에 굳이 fastify를 붙일 필요가 없다.
			- express 사용하면 미들웨어 재사용할 수 있어서 생태계의 도움을 얻을 수 있다.

## 핫 리로딩 설정
- 서버에 변경사항이 생기면 자동으로 리로드되도록 설정할 수 있다.
	- 다음과 같이 모듈을 설치한다
	```bash
	npm i --save-dev webpack-node-externals run-script-webpack-plugin webpack
	```

- 이후 다음과 같이 파일 ``` webpack-hmr.config.js``` 파일을 루트 디렉토리에 추가하고, 다음을 넣는다.
	```typescript

const nodeExternals = require('webpack-node-externals');
const { RunScriptWebpackPlugin } = require('run-script-webpack-plugin');

module.exports = function (options, webpack) {
  return {
    ...options,
    entry: ['webpack/hot/poll?100', options.entry],
    externals: [
      nodeExternals({
        allowlist: ['webpack/hot/poll?100'],
      }),
    ],
    plugins: [
      ...options.plugins,
      new webpack.HotModuleReplacementPlugin(),
      new webpack.WatchIgnorePlugin({
        paths: [/\.js$/, /\.d\.ts$/],
      }),
      new RunScriptWebpackPlugin({ name: options.output.filename, autoRestart: false }),
    ],
  };
};
```
- 또, ```main.ts``` 파일에 다음과 같이 로직을 정의한다.
```typescript
declare const module: any;

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  await app.listen(3000);

  if (module.hot) {
    module.hot.accept();
    module.hot.dispose(() => app.close());
  }
}
bootstrap();
```
- 마지막으로 실행 과정을 단순화하기 위해 package.json 파일에 다음과 같이 추가한다.
	- 