---
title: react hooks는 그저 arrays다
---
이전에 이해하고 넘어갔던 내용인데 공부하다가 고취시키는 좋은 자료가 있어 내용을 가져와 정리해본다.
react hooks는 렌더링시마다 closer를 기반으로 한 singleton 배열이다. 컴포넌트 내에서 순서대로 호출되는 각각의 모든 훅은 순서(index)를 갖게 되며 그 순서에 따라 결정된 값을 가지고 있을 뿐이다.
- source: https://www.swyx.io/hooks#not-magic-just-arrays
- source example by [codesandbox](https://codesandbox.io/p/sandbox/react-hooks-not-magic-just-arrays-7n96dg)
