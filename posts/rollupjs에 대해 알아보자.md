---
title: rollupjs에 대해 알아보자
---
# 왜 rollup을 쓰는거지?


## 다음과 같은 코드는 왜 있는것일까?
```js
Object.defineProperty(exports, '__esModule', { value: true });
```
commonjs/AMD/UMD 모듈 시스템에서는 import 구문을 인식하지 못한다. 해당 환경에서 