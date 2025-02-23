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

