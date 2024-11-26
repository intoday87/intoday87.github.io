---
title: codemod에 대해 정리해보자
---
접근이 쉬운 [Codemod를 이용한 코드 변환 medium](https://medium.com/@bjrnt/codemod%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%BD%94%EB%93%9C-%EB%B3%80%ED%99%98-bf04e894f3f1) 을 보고 비교적 쉽게 접근이 가능했다
source를 AST로 분석하고 AST node를 기반으로 library가 transform하는 코드를 작성해서 변경하는 방식으로 보인다

https://astexplorer.net/
을 들어가서 `Transform`을  jscodeshift로 변경해보자. [Transform module](https://github.com/facebook/jscodeshift?tab=readme-ov-file#transform-module)을 보면 transform의 코드를 작성하는 예제가 나와 있다
