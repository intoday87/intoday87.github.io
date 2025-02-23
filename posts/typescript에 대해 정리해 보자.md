---
title: typescript에 놓치고 있는 부분을 정리해 보자
---
## any와 unknown에 대한 차이점

`unknown`은 `any`와 달리 `any` type이 아닌 다른 모든 타입에 할당될 수 없는 차이가 있다. 어떤 타입이든 할 당될 수 있는 `any` 보다는 `unknown`을 선호해야겠다.

|     any     | unknown | object | void | undefined | null | never |     |
| :---------: | :-----: | :----: | :--: | :-------: | :--: | :---: | --- |
|    any →    |         |   ✓    |  ✓   |     ✓     |  ✓   |   ✓   | ✕   |
|  unknown →  |    ✓    |        |  ✕   |     ✕     |  ✕   |   ✕   | ✕   |
|  object →   |    ✓    |   ✓    |      |     ✕     |  ✕   |   ✕   | ✕   |
|   void →    |    ✓    |   ✓    |  ✕   |           |  ✕   |   ✕   | ✕   |
| undefined → |    ✓    |   ✓    |  ✓   |     ✓     |      |   ✓   | ✕   |
|   null →    |    ✓    |   ✓    |  ✓   |     ✓     |  ✓   |       | ✕   |
|   never →   |    ✓    |   ✓    |  ✓   |     ✓     |  ✓   |   ✓   |     |
source: https://www.typescriptlang.org/docs/handbook/type-compatibility.html#any-unknown-object-void-undefined-null-and-never-assignability

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

`Vector3D`의 key를 추출해서 값을 꺼내는데 `axis`로 `v`의 인덱스에 접근하는게 왜 문제가 되는것일까? 단순히 보면 `vector3D`의 봉인된 키값들만 꺼내서 처리하니 문제가 없을것 같아보인다. 그 이유는 구조적 타이핑(structural typing). js의 duck typing처럼 알맞은 속성과 메서드가 있어서 인터페이스를 만족한다면 해당 객체로 보겠다는 개념을 typescript에서는 훼손하지 않고 유지하고 있는 셈이다. 그래서 다음과 같은 코드가 가능하다.
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
duck typing과 마찬가지고 typescript의 구조적 타이핑 역시 인터페이스가 호환이 되면 타입을 지정된 타입이 아니여도 오류를 발생하지 않는다. 자바와 c++을 해본 사람이라면 같은 class의 구조라고 하더라도 선언된 class가 다르면 오류를 발생하는 것과 달라 당황스러울 수 있다.  js의 본질을 유지하면서 typescript는 js의 런타임을 설계하기 때문에 타입이 삭제되는 transfile된 결과에서 동작dl  않는다.