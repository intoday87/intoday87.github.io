---
title: typescript에 놓치고 있는 부분을 정리해 보자
---
## any와 unknown에 대한 차이점

`unknown`은 `any`와 달리 `any` type이 아닌 다른 모든 타입에 할당될 수 없는 차이가 있다. 어떤 타입이든 할 당될 수 있는 `any` 보다는 `unknown`을 선호해야겠다

|     any     | unknown | object | void | undefined | null | never |     |
| :---------: | :-----: | :----: | :--: | :-------: | :--: | :---: | --- |
|    any →    |         |   ✓    |  ✓   |     ✓     |  ✓   |   ✓   | ✕   |
|  unknown →  |    ✓    |        |  ✕   |     ✕     |  ✕   |   ✕   | ✕   |
|  object →   |    ✓    |   ✓    |      |     ✕     |  ✕   |   ✕   | ✕   |
|   void →    |    ✓    |   ✓    |  ✕   |           |  ✕   |   ✕   | ✕   |
| undefined → |    ✓    |   ✓    |  ✓   |     ✓     |      |   ✓   | ✕   |
|   null →    |    ✓    |   ✓    |  ✓   |     ✓     |  ✓   |       | ✕   |
|   never →   |    ✓    |   ✓    |  ✓   |     ✓     |  ✓   |   ✓   |     |
> source: https://www.typescriptlang.org/docs/handbook/type-compatibility.html#any-unknown-object-void-undefined-null-and-never-assignability

## 'string' 형식의 식을 'SomeType' 인덱스 형식에 사용할 수 없으므로 요소에 암시적으로 'any' 형식이 있습니다

자 다음과 같은 코드가 있다고 해보자

```ts
interface Vector3D {
  x: number
  y: number
  z: number
}

function calculateLengthL1(v: Vector3D) {
  let length = 0
  for (const axis of Object.keys(v)) {
    const coord = v[axis]
    // ~~~~~~~ 'string'은 'Vector3D'의 인덱스로 사용할 수 없기에
    // 엘리먼트는 암시적으로 'any' 타입입니다.
    length += Math.abs(coord)
  }
  return length
}
```
> source: o'reilly effective typescript - Dan vanderkam

`Vector3D`의 key를 추출해서 값을 꺼내는데 `axis`로 `v`의 인덱스에 접근하는게 왜 문제가 되는것일까?! 단순히 보면 `vector3D`의 봉인된 키값들만 꺼내서 처리하니 문제가 없을것 같아보인다. 그 이유는 구조적 타이핑(structural typing) 때문이다. js의 duck typing처럼 알맞은 속성과 메서드가 있어서 인터페이스를 만족한다면 해당 객체로 보겠다는 개념을 typescript에서는 훼손하지 않고 유지하고 있는 셈이다. 그래서 다음과 같은 코드가 가능하다
```ts
interface NamedVector {
	name: string
	x: number
	y: number
	z: number
}
const v: NamedVector = { name: 'name', x: 1, y: 2, z: 3 }
calculateLengthL1(v) // NaN
```
결과는 `NaN`이 발생한다. 사실은 정확하게 타입 오류를 잡아 주고 있는 셈이다
js의 duck typing과 마찬가지로 typescript의 구조적 타이핑 역시 인터페이스가 호환이 되면 지정된 타입이 아니여도 오류를 발생하지 않는다. 자바와 c++을 해본 사람이라면 class의 구조가 같다고 하더라도 선언된 class가 다르면 오류를 발생하는 명목적 타이핑(Nominal Typing)과 다른 점으로 매우 당황스러울 수 있다.  typescript는 js의 본질을 유지하면서 js의 런타임을 설계하기는 용도기 때문에 타입이 삭제되는 transfile된 결과에서 js의 동작이 바뀌는것은 아니라는 점에 유의해야 한다

해결 방법은 두 가지 정도가 있는데 다음은 [type predicate](https://www.typescriptlang.org/docs/handbook/2/narrowing.html#using-type-predicates)를 사용해서 해결하는 방법이 있다
```ts
function isVector2D(v: Vector2D): v is Vector2D { // 함수 결과가 true면 v는 Vector2D로 type guard
	return typeof v.x === 'number' && typeof v.y === 'number' && !v.hasOwnProperty('z')
}
```

또 한 가지는 taged type을 이용하는 방법인데 타입에 국한되지 않으므로 타입 구분용 필드가 추가되는 부분에 거부감이 있을 수 있다

```ts
interface Vector2D {
	kind: 'vector2D' // 타입 구분을 위한 tag를 명시하여 validation
	x: number
	y: number
}
interface Vector3D {
	kind: 'vector3D',
	x: number
	y: number
	z: number
}
const v3: Vector3D = {kind: 'vector3D', x:2, y:3, z:4} 
calculateLengthL1(v3)
// Argument of type 'Vector3D' is not assignable to parameter of type 'Vector2D'. Types of property 'kind' are incompatible. Type '"vector3D"' is not assignable to type '"vector2D"'.
```

 하지만 객체 리터럴로 넘길 경우 충족하는 프로퍼티가 모두 있음에도 불구하고 타입간 호환성을 따지지 않고(타입이 없는 객체 리터럴 이라서) 초과 속성 검사(excess process checking)가 동작되어 이 타입간 호환성에 대해 간과하고 넘어가기 쉽다
 
```ts
calculateLengthL1({ x:1, y:2, z:3 }) // 초과 속성 검사로 'z'가 없다고 타입 오류 발생
```

## Intersection Types

타입의 intersection(`&`)은 집합으로 교집합에 해당하는데 왜 두 타입간 intersection은 서로간 모든 타입을 포함하게 되는것일까? 오히려 합집합을 의미하는 union(`|`)이어야 하지 않을까?
이 대답에 명확하게 설명하는 글을 보지 못해서 여지것 [intersection-types](https://www.typescriptlang.org/docs/handbook/unions-and-intersections.html#intersection-types)에서 설명하는 예시대로만 표면적으로 사용을 해왔던것 같다. 하지만 o'reilly effective typescript - Dan vanderkam의 책을 읽으면서 조금 더 그 이유에 대해 와 닿게 되는것 같다.  이해한대로 적어보자면 구조적 타이핑 관점에서 항상 주의를 해야할 것은 타입 하나는 명시된 속성외에 다른 속성을 가질수 있다라는 점이다. 그 점이 간과되지 않아야 intersection이 두 타입간의 모든 속성을 반환하는 점을 이해할 수 있을것이다. 다음은 내가 이해한 예시다

```ts
// 두 타입이 모두 숫자의 범위를 가진 속성만 가지고 있다고 가정해보자
interface A {
    a: number
    b: number
}
interface B {
    c: number
}
interface StructuredTypedA {
    a: number
    b: number
    c: number // 구조적 타이핑에서 항상 타입은 다른 속성을 가질수 있다
}
interface StructuredTypedB {
    a: number
    b: number
    c: number
    d: number // 구조적 타이핑에서 항상 타입은 다른 속성을 가질수 있다
}

const ea: StructuredTypedA = {
    a: 1,
    b: 2,
    c: 3,
}
const a: A = ea
const eb: StructuredTypedB = {
    a: 1,
    b: 2,
    c: 3,
    d: 4,
}
const b: B = eb
type AAndB = A & B
const aAndB: AAndB = { // 구조적 타이핑 관점에서 A, B의 타입의 범위의 교집합을 구하면 A, B의 선언한 모든 속성이 포함된 형태가 된다
    a: 1,
    b: 2,
    c: 3,
}
```
즉 요약하자면 구조적 타이핑 관점에서 선언된 속성 외에 다른 속성을 가질수 있다고 가정하면 선언된 속성외 어떤 속성은 intersection(`&`)하는 다른 속성이 있을수도 있다고 전제가 되기 때문에 결국에는 교집한은 A, B 속성이 모두 포함된 형태가 된다

## Union types

다음과 같은 케이스에서 Union을 합집합이라고 생각하면 A, B의 모든 키가 포함되어야 할 것 같은데 왜 결과가 `never`일까?

```ts
interface A {
	a: number
}
interface B {
	b: number
}
type u = A | B // A가 되거나 B가 되거나
type k = keyof(A | B) // never -> 구조적 타이핑 관점에서 추가적인 속성이 있을수 있는 타입에서 A | B = U(전체집합)이 된다. 그러므로 A, B의 union은 키를 특정할 수 없어 never가 된다
type kab = keyof A | keyof B // `a` | `b` -> 각 A,B의 키 값의 결과는 타입이 아닌 단 하나의 값을 가질 수 있는 리터럴이라 우리가 생각했던 union에 대한 결과가 된다
type kab2 = keyof (A & B) // `a` | `b` // 구조적 타이핑관점에서 intersection은 A, B의 속한 속성들의 집합이 된다. 그러므로 keyof를 하면 A, B에 선언한 모든 속성이 결과가 된다

const O = {
	a: 1,
}
const AorB: A | B = O // 정상. 전체 집합 U에 대해 a만 있어도 상관 없다. A가 되거나 B가 되거나 이기 때문
const AandB: A & B = O
//    ~~~~~ 
//    b가 없다
```

단순히 union이라고 생각 하면 A, B에 선언된 모든 키만 합산되어 keyof에서 나열되어야 할 것 같지만 never인 이유는 구조적 타이핑 관점에서 다시 생각해봐야 한다. *모든 타입은 선언된 속성 말고도 추가적인 속성을 포함할 수 있다*  의 관점으로 생각해보면 `A | B`는 추가적은 속성을 포함한 `U`(전체 집합)이 되기 때문에 각각의 타입에 속한 속성만 있다고 할 수 없어 never가 된다 

## 값 쌍과 같은 타입간 호환은 length 속성도 체크한다

	```ts
const pair: [number, number] = [1, 2]
const triple: [number, number, number] = pair
//    ~~~~~~
// Type '[number, number, number]' is not assignable to type '[number, number]'. Source has 3 element(s) but target allows only 2.(2322)
```
물론 string triple에 할당하려고 하면 number와 string타입이 달라 오류로 체크한다
## 타입스크립트 용어와 집합 이론 용어 사이의 대응 관계
| 타입스크립트 용어              | 집합 용어                   |
| ---------------------- | ----------------------- |
| never                  | ϕ(공집합)                  |
| 리터럴 타입                 | 원소가 1개인 집합              |
| 값이 T에 할당 가능            | 값 ∈ T (값이 T의 원소)        |
| T1이 T2에 할당 가능          | T1 ⊆ T2 (T1이 T2의 부분 집합) |
| T1이 T2를 상속             | T1 ⊆ T2 (T1이 T2의 부분 집합) |
| T1 \| T2 (T1과 T2의 유니온) | T1 ∪ T2 (T1과 T2의 합집합)   |
| T1 & T2 (T1와 T2의 인터섹션) | T1 ∩ T2 (T1과 T2의 교집합)   |
| unknown                | 전체(universal) 집합        |
> source: o'reilly effective typescript - Dan vanderkam
## typescript에서 type이 되지 못하는 값이 있다?

- [ ]  가끔 Exclude를 사용해서 일부 타입을 제외할 수는 있지만, 그 결과가 적절한 타입스크립트 타입일 때만 유효
```ts
type NonZeroNums = Exclude<number, 0>; // 타입은 여전히 number
type T = Exclude<string|Date, string|number>; // 타입은 Date
```

- [ ] x와 y 속성 외에 다른 속성이 없는 객체는 타입스크립트 타입에 존재하지 않는다 
```ts
interface Type {
	a: number
	b: number
}
const t: Type = {
	a: 1,
	b: 2,
	c: 3, // exceed property checking
//  ~
//  Object literal may only specify known properties, and 'c' does not exist in type 'Type'.(2353)
}
const temp = {
	a: 1
	b: 2
	c: 3
}
const t2: Type = temp // 정상. temp는 t2의 부분 집합인 a, b를 모두 포함하고 있어서 정상
```

## javascript runtime 타입
값 앞에 `typeof`를 붙이면 js의 런타임 타입을 얻을 수 있다. js 타입은 매우 단순하다.
객체(object)를 제외하고 기본형에 대한 타입은 boolean, string, number, null, symbol, bigint, undefined 정도 이다. 기본형은 immutable하고 메서드가 없다. 하지만 우리는 흔히 메서드가 있는 래퍼 객체처럼 사용 해서 객체인줄 착각할 수 있지만 래퍼 객체로 자동 변환되어 처리되기 때문에 객체인 줄 오해하는것 뿐이다
```ts
const x = 'string'
x.language = 'en'
// en
x.language
// undefined. 래핑된 객체는 이미 버려짐
```

기본형 타입에 메서드를 사용하려면 일단 객체로 변환되고 사용된 객체는 바로 버려진다

```js
const x = new String('string')
x.language = 'en'
x.language
// en
```

그렇기 때문에 typescript는 기본형과 래퍼객체에 대한 타입을 분리해 놓았다

```ts
const s: string = 'string'
const sw: String = new String('string')

s === sw // false
s == sw // true s를 래퍼객체로 타입 변환
new String('string') === new String('string') // false 참조가 다른 객체
'string' === 'string' // immutable한 기본형은 true
```

이런 엄격한 동등 비교와 느슨한 동등 비교에서 차이가 있어서 왠만하면 래퍼 객체로 사용하지 않는것이 좋다.
이러한 이유로 메서드에서 래퍼 객체 대신 기본형 타입을 받는다

```ts
const target: String = new String('love')
'i love you'.search(target)
//                  ~~~~~~
//                  기본형 타입을 받는데 래퍼 타입이 들어와서 타입 오류

function log(v: String) {}
log('some log') // 정상
log(new String('some log')) // 정상

function isIncluded(v: String) {
	return ['a', 'b'].includes(v)
	//                         ~
	//                         기본형 타입을 받는데 래퍼 타입이 들어왔음 오류
}
```

그리고 기본형은 래퍼타입에 할당할 수 있기 때문에 조심해야 한다

```ts
const s: String = 'string'
const b: Boolean = true
const n: Number = 1
const n: BigInt = 1 // 이것만 오류나네;;
```

## Exceed property checking (잉여 속성 체크)

구조적 타이핑 관점에서는 다음과 같은 코드는 에러가 없어야 한다.  **타입은 추가적인 속성을 포함할 수 있다는 전제하에 있다**는 것이 구조적 타이핑의 관점이기 때문이다
```ts
interface T {
	a: number
	b: number
}
// 객체 리터럴에서만 잉여 속성 체크가 동작한다
const v: T = {
	a: 1,
	b: 2,
	c: 3,
//  ~
//  Object literal may only specify known properties, and 'c' does not exist in type 'T'.(2353) -> c는 잉여 속성 체크에서 확인된 없는 속성
}

function sf(v: T) {}
sf({ a: 1, b: 2, c: 3})
//               ~
//               c는 없는 속성
```

하지만 객체 리터럴이 아니라 임시 변수나 별도의 타입간 할당 가능한 부분 집합의 타입이라면 구조적 타이핑 관점대로 동작한다

```ts
const o = {
	a: 1
	b: 2
	c: 3
}
const v: T = o // 정상

interface SubT {
	a: number
	b: number
	c: number
}
const st: SubT = {
	a: 1
	b: 2
	c: 3
}
const v2: T = st // 정상. SubT는 T의 부분 집합. 즉 구조적 타이핑에서 할당 가능한 관계
```

잉여 속성 체크의 한계와 구조적 타이핑을 이해해야 정확히 그 둘의 차이를 구분할 수 있다. 잉여 속성 체크는 사용자의 실수를 줄여주기 위한 매우 제한적인 기능이다

한 가지 더 에를 들어보자

```ts
interface Options {
	title: string
	darkMode?: boolean
}

const o: Options = {
	title: 'some title',
	darkmode: true,
//  ~~~~~~~~
//  darkmode는 없는 속성입니다. -> 잉여 속성 체크(exceed property checking)
}

const o2: Options = document // 정상
const o3: Options = new HtmlAnchorElement // 정상
```

## 선택적 속성만 가지는 약한(weak) 타입에 적용되는 속성 체크

구조적 타이핑 관점으로 보면 선택적 속성만 가진 타입은 선언되지 않은 다른 필드만 가져도 문제 없이 동작할 것이라 기대할 수 있다. 타입은 다른 속성을 포함할 수 있다는 관점에서 보면 말이다. 하지만 여기서 약한 타입(모든 필드가 옵셔널)의 경우 별도의 공통 속성 체크가 동작해서 구조적 타이핑 관점을 헷갈리기 쉽다

```ts
interface SomePropertyOptional {
	a: number
	b?: number
	c?: number
}

// `d`만 있는 별도의 타입으로 선언하고 객체 리터럴로 초기화 해도 됨
const o = {
	a: 1,
	d: 1,
}

const t: SomePropertyOptional = o // 정상. 구조적 타이핑 관점에서는 o의 타입은 필수인 a가 선언되어 있는 부분 집합. d는 구조적 타이핑 관점에서 얼마든지 선언 포함 가능한 추가 속성
```

하지만 모두 옵셔널인 약한 타입의 경우 별도의 속성 체크가 들어간다

```ts
interface WeakType {
	a?: number
	b?: number
	c?: number
}

const o = {
	d: 1
}

const t:WeakType = o
//    ~
//   Type '{ d: number; }' has no properties in common with type 'WeakType'.(2559)
```

분명 모든 속성이 선택적인 `WeakType`에서는 구조적 타이핑 관점으로 다른 속성만 있는 타입도 정상 동작할 것 같지만 임시 변수에 객체 리터럴을 할당해서 `WeakType`의 변수에 할당했음에도 문제가 생긴다(잉여 속성 체크를 피하기 위해 임시 변수에 객체 리터럴로 초기화). 이 역시 사용자의 타이핑 오류를 줄이기 위한 한계가 명확한 공통 속성 체크이며 잉여 속성 체크와 마찬가지로 한계가 명확하다.  약한 타입의 경우 선언 타입과 값 타입의 공통 속성이 있는지 별도의 체크가 수행된다. **잉여 속성 체크와 다른 점은 약한 타입과 관련된 할당에서 동작한다**. 잉여 속성 체크와 겹치지 않는 부분이다. 이런 공통 속성 체크와 일반적인 타입 체크를 구분한다면 두 가지 metal model을 잡는데 도움이 된다

##  interface와 type의 차이
interface를 언제 써야 하는가? 하는 물음이 항상 있었는데 확실한 예제와 답이 되는 케이스가 책에 있었다
선언적 머지([declaration merging](https://www.typescriptlang.org/docs/handbook/declaration-merging.html))를 예로 들 수 있다. `Array.prototype.`concat의 경우 typescript의 lib/es5.d.ts에서 선언을 찾을수 있다.  하지만 `Array.prototype.find`는 어떤가 lib/lib.es2015.core.d.ts에서 찾을수 있다. 두 가지 메서드가 `Array.prototype`에 있는데 d.ts는 나뉘고 있는 부분에서 짐작할 수 있듯이 tsconfig에 `lib`에 'es2015'의 추가하면 lib/es5.d.ts에 lib/lib.es2015.core.d.ts가 추가되어 선언적으로 머지가 되어 기존 인터페이스를 확장하게 된다


## Readonly
```ts
interface Outer {
	inner: {
		x: number;
	}
}

const o: Readonly<Outer> = { inner: { x: 0 }};

o.inner = { x: 1 };
// ~~~~~ 읽기 전용 속성이기 때문에 'inner'에 할당할 수 없습니다.

o.inner.x = 1; // 정상 x에 readonly를 설정해야만 오류가 발생한다
```

readonly는 얕게 동작한다.
ts는 아직까지 내부의 필드까지 deep 순회를 하면서 readonly를 지원하는 기능은 없다. 하지만 ts-essential에 [DeepReadonly](https://github.com/ts-essentials/ts-essentials/tree/master/lib/deep-readonly)를 사용할 수 있다.  [playground](https://www.typescriptlang.org/play/?jsx=0#code/JYWwDg9gTgLgBAbzgEQKarAJVQQwCYQB2ANgJ5wC+cAZlBCHAEQwDOAtKiy6oTMDsRaMA3AChRwXqijUcAY1RwAwvTA5C5BKLhxCOEKgBccFjCiSA5mJ2pwxCKXQtjSPQeOnzhC5QDaAXTEKcRhSMEU0DGx8IjIVcHVyAF4UdCxcAhJSAB54tQ0APjEAemKdOAA9AH5xPFQ5YhwoRTkiUzhWhI1jSPSYrLzEsVFS5XVCCHgcLmALQjgYCDgAcjdUZbgAI3qcAFduOGB4YBY4HDhm-DZY8jA6cNhSUU780gA6NbgU5dYOLh4+AIWMsSmUlONJmcZnMFktlrYwPZHJwNts5HsDkdDqdzpc8NcsnA7hAHqFnqpEm8EUinF84AFQWNCBMptD5osVmtUTt9oosSczhcMgSyET7tIyS9KdSHE5fAAGfwffSKb4AaXMpzV8gAXtBluJqLtCHI+EQ4HUMAAxZqoHWobIAFTCilQAA8YDw8KcIJsAFb1GAFAAUvr9xmd4QAlD00tFMmQnS6CohtHBRtgzMBUAA3RQwAAWimJpPIa1OdWoklQeDg5rDgbTrUI7RLADkVacUthqMRA28IAB3Qhq1CkFih-1RqFwYMAazHEGocEjqCjDPEOlGNvQ9rFJIl2dO22o0EUtF3lhMqGI1DTp6gs+b7U+S-3YA7BhY0605Q6bXgHMBF2VU639Xw1kCTdymAZdg2DIDiBAuAADIUIWF030Q5CklwpgG1NRhpwAH2IjDwiw4DVTwxgjRNM1CCI1M-x0S0wB3O1UAQqio2scpgh0YI02aGBdigeYAHl-X7C9OMnP1p2mVIogyG4k3CIpRAoIA)로도 확인해볼 수 있으니 참고해보자!b

## 넓히기(Widening)

타입스크립트는 타입 추론시 선언된 변수가 최종 사용시점까지 파악해서 추론하는 방식이 아닌 선언되는 시작 시점에 타임을 추론한다. 그리고 타입추론은 생각보다 좁다(구체적이다)

```ts
let x = 'x'

function get(k: 'x' | 'y' | 'z') {
	get.o[k]
}
get.o = {} as Record<'x' | 'y' | 'z', any>

get(x)
// string은 'x'에 할당할 수 없다. let을 const로 바꾸면 넓히기가 적용되어 string 타입으로 추론되지 않고 타입이 'x'로 한정된다
```

let인 경우 넓히기로 string 타입으로 추론되는 이유가 let의 재할당 가능성에 있다. 그리고 최종 사용시점까지 추적하는 정적 언어의 타입 분석이 아니기 때문이기도 하다. 현재 코드만 보면 최종 사용시점까지 파악해서 추론한다면 string이 아닌 'x'가 되었을 것

그럼 const가 만능이지는 않다

```ts
const mixed = ['x', 1] // (string|number)[]

const v = { // { x: number }
	x: 1,
};

v.x = 3;
v.x = '3';
//~ string 할당 안됨
v.y = 4;
//~ y라는 키 없음
v.name = 'Pythagoras';
//~~~~ name이라는 키 없음
```

const라도 객체로 만든 경우 let으로 넓히기 알고리즘이 동작한다. x는 재할당 가능성을 두고 number로 추론한다.  객체의 추가 할당에 관해서는 js에서는 이런 코드가 동작에는 문제가 되지 않겠지만 타입을 고려하면 모든 필드는 한 번에 만들어야 한다.
이러한 이유는 너무 구체적으로 타입을 추론하는 경우 v의 경우 `{ readonly x: 1 }` 인 경우 의도와는 다른 잘못된 추론으로 불필요하게 타입을 선언하는 코드들이 늘어날 것이다(그것 보다는 x에 재할당 가능성을 열어둔 채 놓아두는게 타입 선언을 줄일수있다고 판단)

다음과 같은 방법으로 객체의 경우 넓히기를 제한하는 방법도 있다

```ts
const v = { // { x: 1 }
	x: 1 as const
}

const v2 = { // { readonly x: 1, readonly y: 2 }
	x: 1
	y: 2
} as const
```
값 뒤에 as const를 붙이면 최대한 좁은 타입으로 추론을 제한한다

## 좁히기

넓히기와 반대로 조건에 따라 타입이 제한되어 좁혀질 수 있다

```ts
interface A { a: number }
interface B { b: number }
function pickAB (ab: A | B) {
	if ('a' in ab) {
		ab // A
	} else {
		ab // B
	}
}

function contains(texts: string | string[]) {
	if (Array.isArray(texts)) {
		return texts // string[]
	} else {
		return [texts] // string
	}
}

function includes(text: string, search: string | RegExp) {
	if (search instanceof RegExp) {
		return !!search.exec(text)
	} else {
		return text.includes(searc)
	}
}
```

 자세히 안보면 헷갈릴 수 있는 케이스

```ts
function foo(x?: number | string | null) {
	if(!x) {
		x // number | string | null | undefined
	}
}
```
number에서 `0`과 string의 `''` 빈 문자열이 false로 평가되기 때문에 같이 false로 평가되는 `undefined`, `null`과 같이 통과되어 원본 타입이 좁혀지지 않았다

태그 기법을 이용해 타입을 좁히는 방법

```ts
interface A { type: 'A' }
interface B { type: 'B' }

function resolve(v: A | B) {
	switch(v.type) {
		case 'A':
			v // A
			break
		case 'B':
			v // B
			break
	}
}
```