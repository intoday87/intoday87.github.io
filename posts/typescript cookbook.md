---
title: typescript cookbook
---
# typescript cookbook 🧑🏽‍🍳

> "Cookbook"은 일반적으로 여러 가지 요리를 만드는 데 필요한 레시피를 모아 놓은 책을 의미합니다. 그러나 프로그래밍 문맥에서 "cookbook"은 특정 문제를 해결하는 방법을 간단하고 명확하게 설명한 코드 샘플 모음집을 가리키기도 합니다. 예를 들어, 'Python Cookbook'은 Python 프로그래밍 언어로 문제를 해결하는 다양한 방법을 제시하는 책일 수 있습니다.
> by chatgpt

## object에서 key에 해당하는 union type 얻기

```typescript
const O = { 
	A: 1, 
	B: 2, 
	C: 3 
} as const
type KeysOfO = keyof typeof O // 아래 코드는 KeysOfO를 확인하는데 사용됩니다. console.log(KeysOfO); // "A" | "B" | "C"
```

## enum computed value  >= 5.0
[All `enum`s Are Union `enum`s](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-5-0.html#all-enums-are-union-enums) 참고
```ts
const PREFIX = "prefix"
enum E {

  Blah = Math.random()
  A = `${PREFIX}-A`

}
```

## 배열의 문자열을 키로 하는 타입 만들기

```ts
const a = ['a', 'b', 'c', 'd', 'e'] as const

// 배열의 값들을 키로 사용하는 타입 생성
type AKeys = typeof a[number]

// 타입 확인
const example: Record<AKeys, string> = {
  a: "value1",
  b: "value2",
  c: "value3",
  d: "value4",
  e: "value5",
}
```