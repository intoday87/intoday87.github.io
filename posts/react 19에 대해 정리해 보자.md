---
title: react 19에 대해 정리해 보자
---
[react 19 blog](https://react.dev/blog/2024/12/05/react-19)를 읽고 이해한 내용을 정리해 보자

## [improvements to suspense](https://react.dev/blog/2024/04/25/react-19-upgrade-guide#improvements-to-suspense)
컴포넌트가 트리에서 suspense 될 때 sibling 컴포넌트가 먼저 렌더링되고 suspense된 컴포넌트가 commit phase로 넘어갔는데 react 19에서는 suspense된 컴포넌트가 sibling보다 먼저 commit phase에 돌입한다.
즉 suspense의 보여짐이 더 빨라진다

## Server Functions
### [`'use server'` directive](https://react.dev/reference/rsc/use-server#use-server)
- backtic은 안된다. single, double quote만 된다
- async function body 최상단에 마크한다. 파일 최상단 마크시 export되는 모든 것들이 Server Functions로 취급된다
- client에서 호출이 가능하며 실행은 서버에서 된다
	- client에 `Symbol.for`로 global symbol storage에 관리되는 키 값으로 같은 키 값은 동일한 레퍼런스를 가리킨다
	- client 호출시 xhr로 서버에 요청해서 serialized된 결과 값을 받아 처리한다. 그래서 serialized 가능한 props를 넘겨야 한다 
		 [Serializable arguments and return values](https://react.dev/reference/rsc/use-server#serializable-parameters-and-return-values "Link for Serializable arguments and return values")