---
title: nodejs package에 대해 정리해 보자
---
라이브러리를 제공하는(package author) 입장에서 사용측의 cjs, esm 환경을 둘 다 지원해야 하는 상황에서 필요한 개념과 지식을 까먹어도 빠르게 생각 나게끔 정리를 해보자
# [Dual CommonJS/ES module packages](https://nodejs.org/api/packages.html#dual-commonjses-module-packages)
- commonjs -> `main` 필드
- esmodule(esm) -> `module` 필드

## [conditional exports](https://nodejs.org/api/packages.html#conditional-exports)
- `import` 환경(esm), `require` 환경(cjs)인 경우 entry point를 개별로 둘 수 있다