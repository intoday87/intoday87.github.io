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
const aAndB: AAndB = { // 구조적 타이핑 관점에서 A, B의 타입의 범위의 교집합을 구하면 A, B의 모든 속성이 포함된 형태가 된다
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
type k = keyof(A | B) // never -> 구조적 타이핑 관점에서 추가적인 속성이 있을수 있는 타입에서 A | B = U(전체집합)이 된다. 그러므로 A, B의 union은 키를 특정할 수 없어 never가 된다
type kab = keyof A | keyof B // `a` | `b` -> 각 A,B의 키 값의 결과는 타입이 아닌 리터럴이라 우리가 생각했던 union에 대한 결과가 된다
type kab2 = keyof (A & B) // `a` | `b` // 구조적 타이핑관점에서 intersection은 A, B의 속한 속성들의 집합이 된다. 그러므로 keyof를 하면 A, B에 선언한 모든 속성이 결과가 된다
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
```