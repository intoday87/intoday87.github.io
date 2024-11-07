---
title: rollupjs에 대해 알아보자
---
# 왜 rollup을 쓰는거지?
TODO

## transfile된 코드에서 다음과 같은 코드는 왜 있는것일까?

```
```js
Object.defineProperty(exports, '__esModule', { value: true });
```

commonjs/AMD/UMD 모듈 시스템에서는 import 구문을 인식하지 못한다. 

```js
import foo from 'foo'
```

와 같은 경우 esm이 아닌 환경에서는 어떻게 export를 해줘야 할까?

```javascript
var __importDefault = (this && this.__importDefault) || function (mod) {
    return (mod && mod.__esModule) ? mod : { "default": mod };
}
exports.__esModule = true;
var bar_1 = __importDefault(require("bar"));
```
출처: [stackoverflow](https://stackoverflow.com/questions/50943704/whats-the-purpose-of-object-definepropertyexports-esmodule-value-0)


### plugins의 순서? hook?
TODO
단순 배열의 순서대로 동작하는게 아니다 배열안에서 플러그인이 각 훅을 가지고 있고 그 훅과 일치하는 동일 플러그인 라이프사이클에서 순서대로 동작되는 것으로 보인다
