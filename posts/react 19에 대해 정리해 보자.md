---
title: react 19에 대해 정리해 보자
---
[react 19 blog](https://react.dev/blog/2024/12/05/react-19)를 읽고 이해한 내용을 정리해 보자

## [improvements to suspense](https://react.dev/blog/2024/04/25/react-19-upgrade-guide#improvements-to-suspense)
컴포넌트가 트리에서 suspense 될 때 sibling 컴포넌트가 먼저 렌더링되고 suspense된 컴포넌트가 commit phase로 넘어갔는데 react 19에서는 suspense된 컴포넌트가 sibling보다 먼저 commit phase에 돌입한다.
즉 suspense의 보여짐이 더 빨라진다

 [New React DOM Static APIs?]()