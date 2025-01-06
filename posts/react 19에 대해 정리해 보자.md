---
title: react 19에 대해 정리해 보자
---
[react 19 blog](https://react.dev/blog/2024/12/05/react-19)를 읽고 이해한 내용을 정리해 보자

## [improvements to suspense](https://react.dev/blog/2024/04/25/react-19-upgrade-guide#improvements-to-suspense)
컴포넌트가 트리에서 suspense 될 때 sibling 컴포넌트가 먼저 렌더링되고 suspense된 컴포넌트가 commit phase로 넘어갔는데 react 19에서는 suspense된 컴포넌트가 sibling보다 먼저 commit phase에 돌입한다.
즉 suspense의 보여짐이 더 빨라진다

## breaking changes
렌더링시 에러 발생에 대해 re-thrown을 방지하려고 별도의 에러 핸들러 인터페이스를 만듬
```tsx
const root = createRoot(container, {  

onUncaughtError: (error, errorInfo) => {  

// ... log error report  

},  

onCaughtError: (error, errorInfo) => {  

// ... log error report  

}  

});
```
[codesandbox](https://codesandbox.io/p/sandbox/react-19-error-5qlgmh)에 테스트 예제 작성해 놓음
말 그대로 렌더링시 발생하는 에러에 대해서만 ErrorBoundary가 catch(`componentDidCatch`)하느냐 안하느냐로 두 핸들러로 요청이 된다. `componentDidCatch`에서 호출되는 에러가 `onCaughtError`에서도 호출된다. ErrorBoundary에서 `componentDidCatch`를 제거해도 `onCaughtError`가 잘 호출된다.
## Server Functions
### [`'use server'` directive](https://react.dev/reference/rsc/use-server#use-server)
- backtic은 안된다. single, double quote만 된다
- async function body 최상단에 마크한다. 파일 최상단 마크시 export되는 모든 것들이 Server Functions로 취급된다
- client에서 호출이 가능하며 실행은 서버에서 된다
	- client에 `Symbol.for`로 global symbol storage에 관리되는 키 값으로 같은 키 값은 동일한 레퍼런스를 가리킨다
	- client 호출시 xhr로 서버에 요청해서 serialized된 결과 값을 받아 처리한다. 그래서 serialized 가능한 props를 넘겨야 한다 
		 [Serializable arguments and return values](https://react.dev/reference/rsc/use-server#serializable-parameters-and-return-values "Link for Serializable arguments and return values") 참고
			- undefined가 되네?

## [StrictMode chnages](https://react.dev/blog/2024/04/25/react-19-upgrade-guide#strict-mode-improvements)
strictMode에서 렌더링 두 번 호출되는 예제. 왜 두 번호출되는지를 좀 정확히 정리가 필요함
pure한 대상은 두 번 호출해서 production에서 문제가 없게끔 하려는 의도로 보이는데
- functional component
- initializer
- updater
https://codesandbox.io/p/sandbox/m26d95
