---
title: js에 대해 놓치고 있는 부분을 정리해 보자
---
## execution context
tbd

Promise에서 resolve까지 호출 순서
iterator 지연
## [generator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Generator)
- iterable protocol
- iterator protocol 

### eager evaluation
즉시 실행되어(execute immediately) 실행 시점을 미룰수가 없다

promise는 eager다
```js
console.log('before promise')
const p = new Promise((resolve, reject) => {
	console.log("in a promsie")
	resolve('resolve promise')
})
p.then((result) => console.log(result) )
console.log('after promise')

// before promise
// in a promise
// resolve promise
// after promise
```
### lazy evaluation
실행 시점을 호출시점까지 미룰수 있다
```js
function* gen(x) {
	console.log('x', x)
	const y = yield x + 2
	console.log('y', y)
	const z = yield x + y
	console.log('z', z)
}
const i = gen(40) // typeof i[Symbol.iterator] === 'function'
// undefined => gen 함수내 console.log가 호출되는게 없음
i.next() // x 40 { value: 42, done: false }
i.next(2012) // y 2012 { value: 2042, done: false }
i.next() // z undefined { value: undefined, done: true }
```

## js spec과 속한 버전
tc39 proposal 페이지에서는 언제 도입되었는지 파악하기 어렵다.  매번 잊어버리고 찾지 말고 표로 정리해보자

파악하는 방법
-  [finished-proposals](https://github.com/tc39/proposals/blob/main/finished-proposals.md)에서 `expected publication year` 에서 확인할 수 있다
	- 말그대로 예상 연도라 왠지 꺼려진다
- [es version browser support - w3cschools](https://www.w3schools.com/js/js_versions.asp)
	- 잘 정리된 js version별 스펙이 description 탭에 명시되어 있다. official name으로 축약되지 않은 ecma 스크립트 연도별 버전도 나온다

어디에 속하는지 헷갈리는 스펙들을 테이블에 추가해보자

| spec                                                            | year |
| --------------------------------------------------------------- | ---- |
| [async functions](https://github.com/tc39/proposal-async-await) | 2017 |

※ typescirpt의 tsconfig의 [`target`](https://www.typescriptlang.org/tsconfig/#target) 필드에서 `ESNext`라는 설정을 볼 수 있는데 이것은 공식적인 버전의 표시가 아니다. 

## [Symbol](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol)
primitive(unique value), weak encapsulation & information hiding
###  [primitive](https://developer.mozilla.org/en-US/docs/Glossary/Primitive)
```js
const sym1 = Symbol('foo')
const sym2 = Symbol('foo')

sym1 === sym2 // false
sym1 === sym1 // true, is not a object instance
```
### weak encapsulation & information hiding
```js
const sym1 = Symbol('foo')
const o = {
  [sym1]: 1
}
Object.keys(o) // []
Object.values(o) // []
o[sym1] // 1
```

### reference identity를 가지는 유일한 primitive
```js
const sym1 = Symbol('foo')
const os = Object(sym1)
sym1 == os // true
sym1 === os // false
typeof sym1 // symbol
typeof os // object
const os2 = Object(sym1)
os == os2 // false
os2 == sym1 // true
```

### global symbol registry
`Symbol.for`로 생성되는 Symbol value는 global symbol registry에 등록된 값으로 registry에 등록된 값을 공유 한다
```js
Symbol.for('foo') === Symbol.for('foo') // true
Symbole.keyFor(Symbol.for('foo')) === 'foo' // true
```

js의 module 시스템과 더불어 사용하기 위한 것으로 생각이 된다. lifetime of program에 걸쳐 unique한 값으로 존재 할 수 있다

```js
// a.mjs
export const a = Symbol.for('foo')

//b.mjs
export const b = Symbol.for('foo')

//index.mjs
import {a} from './a.mjs'
import {b} from './b.mjs'

console.log(a === b) // true
console.log(Symbol.keyFor(a) === Symbol.keyFor(a)) // true 'foo'
```

이와 같은 특성으로 registered symbol은 기존 symbol과 달리 기억해 놓아야 할 점이 몇 가지 있다
1. 엄밀히 말하자면 호출시 매번 유일한 값을 얻지 않는다(달리 말하면 같은 값을 반환할 수 있다)
```js
Symbol('foo') === Symbol('foo') // false
Symbol.for('foo') === Symbol.for('foo') // true
```
2. gc의 대상이 될 수 없다
```js
const w = new WeakMap()
w.set(Symbol('foo'), { im: 'foo' }) // set
w.set(Symbol.for('foo'), { im: 'foo too' }) // type error
```
WeakMap, WeakSet, WeakRef도 마찬가지

## WeakMap
key는 object와 not registered Symbol만 가능. 나머지는 type error
값은 js로 취급될 수 있는 모든것들이 해당한다
```js
const w = new WeakMap()
w.set(Symbol.for('foo'), 1) // Uncaught TypeError: Invalid value used as weak map key
w.set(Symbol('foo'), 2) // set ok
```
strong reference를 생성하지 않음 -> reference count를 늘리지 않아 값이 언제든 gc 될 수 있다는 것

## [null-prototype object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object#null-prototype_objects)
react 19 server component의 props로 지원 안되는 목록 중  null-prototype objects가 있어서 보다가 정리
모든 객체는 object를 상속하지만 object를 상속하지 않는 null-prototype object를 만들수 있다.
```js
const obj = Object.create(null);
const obj2 = { __proto__: null };

```
[`Object.setPrototypeOf(obj, null)`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/setPrototypeOf)과 같이 prototype을 수정해서도 바꿀 수 있다
 어떤 object의 method도 상속하지 않는다

## Hoisting

문득 다시 [mdn](https://developer.mozilla.org/en-US/docs/Glossary/Hoisting)보니 내가 알던 declaraion hosting이 아닌 value hosting이란 조건이 있다.

>1. **Being able to use a variable's value in its scope before the line it is declared. ("Value hoisting")**
>
>2. Being able to reference a variable in its scope before the line it is declared, without throwing a [`ReferenceError`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/ReferenceError), but the value is always [`undefined`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/undefined). ("Declaration hoisting")
>
>3. The declaration of the variable causes behavior changes in its scope before the line in which it is declared.
>
>4. The side effects of a declaration are produced before evaluating the rest of the code that contains it.


오랜만에 보니까 이런게 있었나 라는 내용에 당황😅(또 그세 까먹은것인가..?!)
2. `var`로 선언 하는 경우 블록 스코프룰을 적용받지 않기 때문에 선언 전에 변수의 사용은 선언 호이스팅(declaration hosting)에 따라 스코프의 최상단에 undefined로 초기화 된 값을 참조하게 된다
```js
console.log(a) // undefined
var a = 10

// 다음과 같은 코드와 동일하게 처리 된다
var a = undefined
console.log(a)
a = 10
```

`var`는 블록 스코프가 아닌 함수의 스코프룰을 적용받는다는 것을 기억하자

```js
var a = 1
function f() {
	console.log(a) // undefined
	var a = 10
}
f()

// 블록 스코프로 테스트
var b = 1
{
	console.log(b) // 1 함수 스코프룰을 따른다
	var b = 10
}
```

3. 변수의 선언은 그 선언 보다 먼저 위치한 스코프에 행동 변화를 일으킨다
4. 변수 선언의 부수효과(예: 호이스팅 등)는, 그 선언이 들어있는 코드 블록의 나머지 부분이 실행되기 전에 먼저 일어난다

그렇다면 1번의 뜻은 해석하기에 변수의 선언이 