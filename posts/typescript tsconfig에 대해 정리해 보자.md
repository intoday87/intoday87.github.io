- `noImplicitAny`
	- 타입을 선언하지 않은 변수, 매개변수에 대해 묵시적으로 any로 취급하는것을 하지 않고 타입 체크 오류로 취급한다
	```
		function add(a, b) {
		// 
			return a + b
		}
	```
- [`lib`](https://www.typescriptlang.org/tsconfig/#lib)
	- transfile시 포함시킬 라이브러리
 - `module`