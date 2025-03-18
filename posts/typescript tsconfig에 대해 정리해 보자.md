- `noImplicitAny`
	- 타입을 선언하지 않은 변수, 매개변수에 대해 묵시적으로 any로 취급하는것을 하지 않고 타입 체크 오류로 취급한다
	```ts
		function add(a, b) {
		// 'a' 매개 변수에는 암시적으로 'any' 형식이 포함됩니다.
		// 'b' 매개 변수에는 암시적으로 'any' 형식이 포함됩니다.
			return a + b
		}
	```
- `strictNullChecks`
	- null이 아닌 타입에 null 할당을 허용할 것인지
	```ts
		const a: number = null	
		// 'null' 형식은 'number' 형식에 할당할 수 없습니다
	```
- `noEmitOnError`
	- tsc는 c나 java 컴파일러와 달리 타입 체크이 문제가 있으면 결과물을 생성하지 않지만 ts는 타입 체크와 transfile을 두 가지를 하면서 두 가지는 서로 독립적이기 때문에 기본적으로 타입 체크 오류가 발생해도 transfile된 js 결과를 생성한다. 만약 타입 체크 오류가 발생시 결과물을 발생하지 않으려면 설정한다. 즉 **런타입에 타입체크는 안된다**
	```zsh
	$ tsc index.ts
	index.ts:2:1 - error TS2322: Type 'number' is not assignable to type 'string'.
	
	2 x = 1234
	  ~
	
	
	Found 1 error in index.ts
	$ ls
	index.ts index.js	
	```

## typescript에서 type이 되지 못하는 값이 있다?

- [ ] 정수에 대한 타입
- [ ] x와 y 속성 외에 다른 속성이 없는 객체는 타입스크립트 타입에 존재하지 않습니다