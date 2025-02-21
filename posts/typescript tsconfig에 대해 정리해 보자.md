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
- [`lib`](https://www.typescriptlang.org/tsconfig/#lib)
	- transfile시 포함시킬 라이브러리
 - `module`