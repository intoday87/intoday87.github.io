---
title: js에 모르는 놓치고 있는 부분을 정리해 보자
---
Promise에서 resolve까지 호출 순서
iterator 지연
## generator
### eager
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
### lazy
실행 시점을 호출시점까지 미룰수 있다
```js
function* gen() {
	yield 1
	yield 2
	yield 3
}
const i = gen() // typeof i[Symbol.iterator] === 'function'
```

## js spec과 속한 버전
tc39 proposal 페이지에서는 언제 도입되었는지 파악하기 어렵다.  매번 잊어버리고 찾지 말고 표로 정리해보자

파악하는 방법
1. [finished-proposals](https://github.com/tc39/proposals/blob/main/finished-proposals.md)에서 `expected publication year` 에서 확인할 수 있다
	- 말그대로 예상 연도라 왠지 꺼려진다
2.  [es version browser support - w3cschools](https://www.w3schools.com/js/js_versions.asp)
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
## WeakMap
key는 object와 not registered Symbol만 가능. 나머지는 type error
값은 js로 취급될 수 있는 모든것들이 해당한다
```js
const w = new WeakMap()
w.set(Symbol.for('foo'), 1) // Uncaught TypeError: Invalid value used as weak map key
w.set(Symbol('foo'), 2) // set ok
```
strong reference를 생성하지 않음 -> reference count를 늘리지 않아 값이 언제든 gc 될 수 있다는 것
