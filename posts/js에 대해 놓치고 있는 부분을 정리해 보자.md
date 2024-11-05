---
title: js에 모르는 놓치고 있는 부분을 정리해 보자
---
Promise에서 resolve까지 호출 순서
iterator 지연
generator

## js spec
tc39 proposal 페이지에서는 언제 도입되었는지 파악하기 어렵다.  매번 잊어버리고 찾지 말고 표로 정리해보자

파악하는 방법
1. [finished-proposals](https://github.com/tc39/proposals/blob/main/finished-proposals.md)에서 `expected publication year` 에서 확인. 
	- 말그대로 예상 연도라 왠지 꺼려진다
2.  [es version browser support - w3cschools](https://www.w3schools.com/js/js_versions.asp)
	- 잘 정릳

| spec                                                            | year |
| --------------------------------------------------------------- | ---- |
| [async functions](https://github.com/tc39/proposal-async-await) | 2017 |
