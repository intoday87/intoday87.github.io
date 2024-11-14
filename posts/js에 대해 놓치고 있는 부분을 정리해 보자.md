---
title: js에 모르는 놓치고 있는 부분을 정리해 보자
---
Promise에서 resolve까지 호출 순서
iterator 지연
## generator
promise, 
```js
function* 
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
|                                                                 |      |
※ typescirpt의 tsconfig의 [`target`](https://www.typescriptlang.org/tsconfig/#target) 필드에서 `ESNext`라는 설정을 볼 수 있는데 이것은 공식적인 버전의 표시가 아니다. 
- todo lib과 target의 차이는 무엇인가? https://www.typescriptlang.org/tsconfig/#lib