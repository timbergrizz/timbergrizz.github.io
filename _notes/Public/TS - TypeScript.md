---
title: TS - TypeScript
feed: show
---

# TypeScript
- 타입스크립트는 자바스크립트 기반에서 확장한 코드
	- 자바스크립트에 없던 형태 지정 등의 기능을 제공하게 된다.
		- 자바스크립트에서 그냥 실행되었던 기능들이 그냥 실행되지 않고, 그렇기 때문에 더 잘 작동할 수 있다.

```toc
```

## 변수와 기본 타입

### 변수 선언
#### var
- 변수를 선언한 위치와 상관 없이 스코프의 최상단으로 콜된다.
	- 같은 스코프라면 라인에 상관없이 호출할 수 있다.
	- 그렇다고 할당을 먼저 하고 나중에 선언하는 코드 쓰면 뒤진다.
- function 단위의 선언을 할 수 있지만, block 단위의 선언을 할 수 없다.

#### let
- block 단위의 스코프를 지원하여, Hoisting을 방지한다.
- 새로운 값으로 설정할 수는 있지만 같은 코드에서 여러번 선언될 수 없다.

#### const
- let과 마찬가지로 블락 단위의 스코프를 지원하여 Hoisting을 방지한다.
- 초기화는 되지만 재할당 되지 않는 상수형태가 된다.
		- 객체 리터럴의 속성은 변경할 수 있다.

### 타입 검사와 타입 선언
- 자바스크립트는 점진적 타입 검사를 사용한다.
	- 필요에 따라 타입 선언의 생략을 허용한다.
		- 타입 선언을 생략하면 implicit하게 형변환이 일어난다.
		- 대표적인 타입으로 any로 설명될 수 있다.
			- 어떤 타입의 변수도 받아들이며 타입이 없는 변수도 받아들인다.
- 자바스크립트의 동적 타입
	- 기본 타입 : Number, Boolean, String
	- 객체 타입 : 객체 리터럴, 배열, 내장 객체
- 자바스크립트에서는 타입이 추론된다.
	- 값을 변수에 할당할 때 타입이 정의 되는 것을 동적 타이핑이라고 한다.
	- 자바스크립트는 해당 타입을 보장하기 위해서 타입 검사를 수행해야 한다.
		- 런타임에서 비교 연산을 수행해 성능에 지장이 있을 수 있다.
	- 안전을 위해 해당 함수 인자가 어떤 타입인지 명시하는 주석을 필요로 한다.

#### 기본 타입
- 보편적으로 많이 사용되는 내장 타입
	- string : 문자열
	- number : 정수, 소수를 합친 숫자
	- boolean : 불리언
	- symbol
		- Symbol() 함수를 이용해 생성한 고유하고 수정 불가능한 데이터 타입
		- 객체 속성의 식별자로 사용된다.
			- 객체의 Key가 문자열이나 정수일 때, 프로그래머의 실수로 객체의 key값이 변경될 수 있다.
			- Symbol로 정의할 경우 해당 키가 unique하여 중복되지 않는다.
	- enum
		- number에서 확장된 타입
		- 첫 번째 Enum 요소에는 숫자 0 값이 할당된다.
			- TS에서는 문자열도 할당 될 수 있다.
			- 그 다음 값은 1씩 증가한다.
	- 문자열 리터럴
		- string의 확장 타입
		- 사용자 정의 타입에 정의한 문자열만 할당받을 수 있다.

#### 객체 타입
- 속성을 포함하고 있므며, call signature, const signature 등으로 구성된 타입이다.
	- array : 배열
	- tuple :  걍 튜플
	- function
		- 함수를 타입으로 설정할 수 있으며, 타입 지정의 대상이기도 하다.
		- 타입 지정 대상은 함수로 전달되는 매게 변수왗 최종 리턴 값이 된다.
	- 생성자
		- 하나의 객체가 여러 생성자의 시그니처로 구성될 때 포함할 수 있는 타입
		- 생성자 타입 리터럴을 사용해 정의할 수 있다.

#### 기타
- union : 2개 이상의 타입을 하나의 타입으로 정의
- intersection : 두 타입을 합쳐 하나의 타입을 정의
- void : 여백의 미
- null, undefined
	- 다른 모든 타입의 하위 타입이다.
	- tsconfig로 null 할당을 막을 수 있따.
	- undefined는 아무런 값도 할당되지 않은 상태이며, null은 빈 객체로 초기화된다.
	- non-nullable : null이나 undefined 허용하지 않음
- lookup tye - keyof
	- keyof로 속섣을 포함하는 대상을 찾아 union처럼 동작

#### 타입스크립트 내장 타입
- any
	- 제약 없는 타입. 근데 이거 쓸거면 웨 타입스크립트 씀?
	- noImplicitAny 옵션을 통해 사용을 강제할 수 있다.
- object
	- any타입과 속성의 유무를 검사하는 시점이 다르다.
- Array Type
	- 요소 타입에 대괄호 붙여 사용한다.
- Generic Array Type
	- ```Array<T>``` 형태로 선언할 수 있다.
```typescript
let arr : string [] = ["apple", "banana"];
let gen_arr : Array<int> = [1, 2, 3];
```

## 연산자
타입스크립트에서는 다음과 같은 연산자들이 추가되었다.
- 비교 연산자 ```===```, ```>==``` 
	- 비교 연산자는 자바스크립트로 컴파일됐을 때도 동작을 보장하기 위해 타입 확인하는 것으로 설정하는 것이 좋다.
- 논리 연산자 &&, ||, !
	- 모르시면 큰일나요
	- 객체의 property에 접근할 때는 항상 undefined되었는지 확인한다.
- 조건 연산자 ? : 
- 지수 연산자 ** -> Math.pow() 대체할 수 있다.

### 디스트럭쳐링
- 객체의 구조를 분해한 후, 할당이나 확장 등의 연산을 수행한다.
- 객체 디스트럭쳐링은 객체 리터럴에서 변수명에 대응하는 속성 값을 추출해 변수로 할당할 수 있다.

### 형 지정
- 함수의 타입을 지정해주는 것이 기본적인 타입스크립트의 문법이다.

## 인터페이스
- 인터페이스는 클래스에 속하는 멤버 변수들이 어떠한 값을 가지는지를 나타낸다.
	- 해당 인터페이스에 속하면 그 인터페이스가 가지고 있는 요소를 모두 가지고 있어야 한다.
		- 요소를 빼먹어도, 새로운 값을 추가해도 에러가 발생하게 된다.
		- 멤버 변수를 선택값으로 설정하기 위해서는 다음과 같이 요소 옆에 ```?``` 를 넣어 해결할 수 있다.
		```typescript
			interface User{
				A : string;
				B? :number;
			}
			let user : User = {
				A = "Hello World",
			}
		```

## 함수
- 자바스크립트는 이름을 명시하는 함수인 기명함수와 이름을 명시하지 않는 익명함수로 나뉜다.
	- 변수를 통해 함수를 선언하는 방식이 익명 함수가 된다.
	- 이때, 기명함수는 호이스팅이 발생하여, 함수를 선언하기 전에도 호출할 수 있다.
- 자바스크립트에서는 함수의 반환에서 동적으로 타입을 할당한다.
	- 이때 추론된 타입이 지정된다.
	- 타입이 명시되어 있지 않기 때문에 런타임에서 의도치 않은 타입 변환이 발생할 수 있다.
		- 타입을 신뢰할 수 없어 타입을 검사하는 코드를 필요로 할 수 있다.

- 타입 스크립트는 함수의 매개변수나 변환 타입을 추가해 타입 안전성을 강화한다.
	```ts
	function max(x: number, y: number): number { ... }
	```

### 매개변수의 활용
- Default Parameter
	- 다음과 같이 매개변수에 기본 값을 설정할 수 있다.
	- 이때, 매개변수가 전달되지 않으면 매개변수에 설정된 초깃값으로 값을 초기화한다.
	```ts
	function pow(x : number, y : number = 2) : number { ... }
	```

- Rest Parameter
	- 개수가 정해지지 않는 인수를 배열로 받을 수 있다.
	- 개수가 정해지지 않아 순서가 크게 중요하지 않은 여러개의 요소를 전달할 수 있다.
	```ts
	function colors(a : string, ...rest : string[]) : string { ... }
	```

- Optional Parameter 
	- 선택 매개변수로 지정된 맥대변수는 생략하여 작성할 수 있다.
	- 이걸 왜 타입스크립트에서 필요로 하는지는 모르겠다.
	```ts
	function sum() : number { ... }
	```

- function overload
	- 함수명은 같지만 매개변수와 반환 타입이 다른 함수를 여러 개 선언할 수 있는 특징을 갖는다.
	- 컴파일시 가장 적합한 오버로드를 선택해 컴파일하고, 실행시에는 런타임 비용이 발생하지 않는다.
	``` ts
	function add(a : string, b: string) : string;
	function add(a : number, b : number) : number;
	```


### 익명 함수의 이해와 활용
- 화살표 함수는 ES6 표준에 포함된 익명 함수를 간략하게 표현할 수 있는 방법이다.
	- ``` () => {} ```  형태로 사용할 수 있다.
- 화살표 함수는 익명 함수로서, 매개변수 목록, 화살표, 함수 블록으로 구성된다.
- 화살표 함수는 변수에 할당하지 않고 즉시 호출함수로 이용할 수 있다.
	- 코드를 실행하면 별도의 외부 호출 없이 자체적으로 호출할 수 있다.
	- filter, find 등의 메서드에 이러한 호출을 적용하여 사용할 수 있다.
	``` ts
		const odds = number_list.filter(n => {
			return !(n % 2);
		})
		const numbers2 = number_list.find(n => n === 2)
	```

- 객체 리터럴의 선언과 객체 리터럴 타입의 선언
	- 객체 리터럴은 여러 속성과 값을 한 단위로 묶어서 표현할 수 있는 객체이다.
	- 객체 리터럴의 속성은 key, value는 숫자나 문자열 뿐만 아니라 사용자가 정의한 객체도 할당할 수 있다.
	- 값을 선언하면서 객체 프로퍼티를 참조하려면 this 키워드를 이용할 수 있다.
	```ts
	let person = {
		_name : "happy",
		hello : function(name : string) : string {
			return "Hello, " + ${this.name} + ${name};
		}
	}
	```
	- 객체 리터럴의 hello 속성에 선언된 함수는 함수 내부 스코프에서 다른 객체 속성에 접근하려 할 때 코드 어시스트가 동작하지 않는다.
		- 만약 정의한 객체 리터럴에서 다른 속성을 참조해야 한다면 리터럴의 타입을 선언해 내부를 참조하도록 하여 코드 어시스트가 동작하게 할 수 있다.
		- 객체 리터럴의 타입은 인터페이스를 통해 정의할 수 있다.
		```ts
		interface PersonType {
		  name: string;
		  hello(this: PersonType, name2: string): string;
		}
		
		let typedPerson: PersonType = {
		  name: "Happy",
		  hello: function (this: PersonType, name2: string): string {
			let message = `Hello, ${this.name + name2}`;
			return message;
		  }
		}
		```
		- this 키워드를 사용해서 객체 리터럴의 속성을 참조하는 내부 참조시에 코드 어시스트가 작동한다.
		- 또한 외부에서 hello 함수에 접근할 때도 코드 어시스트가 작동한다.

- 익명 함수의 함수 타입
	- 익명 함수는 변수에도 할당할 수 있다.
	- 익명 함수가 할당된 변수는 타입을 추가할 수 있고, 함수 자체에도 타입이 존재할 수 있다.
	- 할당한 익명 함수에 매개변수 타입과 반환 타입을 추가할 수 있다.
	```ts
	let myConcat = function (str1: string, str2: string): string { ... };							  
	```
	- 익명 함수와 매개변수에 타입을 지정할 수 있지만, 타입을 선언하면 형태가 복잡할 수 있다.
		- 선언된 타입을 별도로 분리해 함수 타입으로 선언하면 확인하기 편해진다.
	```ts
		let myConcat: (str1: string, str2: string) => string = (str1, str2) => { ... };
		type calcType = (a : number, b : number) => number;
	```

### 콜백 함수의 타입 선언과 활용
- 콜백 함수는 또 다른 함수의 매개변수로 전달된다.
	- 콜백 함수를 전달받는 함수는 콜백 함수보다 상위 처리를 담당하여, 고차 함수라고 불린다.
	- 고차 함수에서 콜백 함수를 인수로 받아 사용하려면 고차 함수 이벤트가 끝난 후의 후속 처리를 콜백 함수에서 실행할 수 있다.
		- 자바스크립트의 비동기 처리를 위해 중요한 내용입니다.
		
		```ts
		function echoFunction(message: string, callback) {
		  return callback(message);
		}
		
		let responseMessage = echoFunction("hello world!", message => message);
		console.log(responseMessage); 	//hello world!
		```

### 클래스 선언과 객체 생성
``` ts
class Rectangle{
	x : number,
	y : number,

	constructor(x : number, y : number){
		this.x = x, this.y = y;
	}
	getArea() : number { return this.x * this.y; }
}
```
- 다음과 같이 클래스를 선언할 수 있다. 클래스 타입은 다음의 인터페이스와 동일하다.
```ts
	 interface Rectangle{
		 x : number;
		 y : number;
		 getArea() : number;
	 }
```

- 클래스 내부에서는 생성자를 정의한다.
	- 생성자는 객체를 생성할 때 클래스에 필요한 설정을 매개변수로 전달받아 멤버 변수로 초기화한다.g