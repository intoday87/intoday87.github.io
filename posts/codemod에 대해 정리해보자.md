---
title: codemod에 대해 정리해보자
---
접근이 쉬운 [Codemod를 이용한 코드 변환 medium](https://medium.com/@bjrnt/codemod%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%BD%94%EB%93%9C-%EB%B3%80%ED%99%98-bf04e894f3f1) 을 보고 비교적 쉽게 접근이 가능했다
source를 AST로 분석하고 AST node를 기반으로 library가 transform하는 코드를 작성해서 변경하는 방식으로 보인다

https://astexplorer.net/
을 들어가서 `Transform` 메뉴에서  jscodeshift로 선택해보자. 
4칸의 공간에 왼쪽 상단은 변환 대상 코드를 오른쪽에는 변환 대상 코드가 AST로 분석된 결과를 볼 수 있다
왼쪽 하단에는  [Transform module](https://github.com/facebook/jscodeshift?tab=readme-ov-file#transform-module)을 작성하는 코드란이고 오른쪽 하단은 변환 결과이다

[ast-types def](https://github.com/benjamn/ast-types/blob/master/src/def/core.ts)를 보고 AST 인터페이스 타입을 확인할 수 있다

js환경에서 어떻게든 작업을 좀 해보려고 했지만 타입 지원 없이는 안되더라
transform 파일을 .ts로 변경하고 types 파일을 설치해서 해보자
```json
"ast-types": "^0.14.2",
"@types/jscodeshift": "^0.12.0"
```
