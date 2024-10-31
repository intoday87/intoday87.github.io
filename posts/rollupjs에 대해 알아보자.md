---
title: rollupjs에 대해 알아보자
---
# 왜 rollup을 쓰는거지?


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

## rollup에서 browserslist를 인식해서 polyfill이 포함되도록 해보자

rollupjs는 기본적으로 polyfill을 추가하지 않는다
babel 플러그인과 함께 사용하는 방법이 가이드 된다. `@swc/core`와 `rollup-plugin-swc3`와 같은 플러그인을 이용해