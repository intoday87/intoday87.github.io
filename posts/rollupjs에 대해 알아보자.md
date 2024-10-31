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

## rollup에서 browserslist를 인식해서 polyfill이 포함되도록 해보자

rollupjs는 기본적으로 polyfill을 추가하지 않는다
babel 플러그인과 함께 사용하는 방법이 가이드 된다. `rollup-plugin-swc3`와 같은 플러그인을 이용해 swc를 이용하는 방법도 있지만 polyfill 지원이 babel에 비해 제한적이여서 그런것으로 확인된다

결과적으로 정리하자면 rollup에서 browserslist 지원 범위를 target으로 지정해 해당 지원 브라우저 범위에 polyfill이 필요하다고 판단되면 번들링 결과에 import가 되어 있는 형태로 지원된다. 코드에 따라 필요에 따라 포함되는 방법을 소개한다.

예시에서 가정은 ie 9에서 `Array.prototype.includes`를 지원하지 않으므로 해당 폴리필을 core-js의 
['core-js/modules/es.array.includes.js'](https://github.com/zloirock/core-js/blob/master/packages/core-js/modules/es.array.includes.js)가 include 되는지 확인해보면 될 것 같다

browserslist를 ie 9부터 지원하도록 셋팅 해보자 - https://browsersl.ist/#q=ie+%3E%3D+9

```js
// rollup.config.js
import babel from '@rollup/plugin-babel';
import commonjs from '@rollup/plugin-commonjs';
import resolve from '@rollup/plugin-node-resolve';

export default {
  input: 'src/index.js',
  output: {
    file: 'dist/bundle.js',
    format: 'cjs'
  },
  plugins: [
    babel({
      babelHelpers: 'bundled',
      presets: [
        [
          '@babel/preset-env',
          {
            targets: {
              browsers: ['ie >= 9'], // ie 9부터 지원
            },
            useBuiltIns: 'usage', // 코드에 따라 필요하면 포함
            corejs: 3,
          },
        ],
      ],
    }),
    resolve(),
    commonjs(),
  ],
};
```
config 파일에서 esm 형태로 작성되어 있음을 유의한다

```json
// package.json
{
  "name": "rollup-browserslist",
  "version": "1.0.0",
  "description": "",
  "type": "module",
  "main": "src/index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "packageManager": "pnpm@9.7.1",
  "devDependencies": {
    "@babel/preset-env": "^7.26.0",
    "@rollup/plugin-babel": "^6.0.4",
    "@rollup/plugin-commonjs": "^28.0.1",
    "@rollup/plugin-node-resolve": "^15.3.0",
    "rollup": "^4.24.3"
  }
}
```
rollup.config.js를 esm으로 인식하기 위해 `type: module`로 선언된 것에 주목한다

```js
// src/index.js
import foo from './foo.js';

const arr = [1,2,3]

console.log(arr.includes(4))

export default function () {
	console.log(foo);
}
```

```js
// dist/bundle.js
'use strict';

require('core-js/modules/es.array.includes.js'); // 번들 결과에 자동으로 polyfill 추가

var foo = 'hello world!';

var arr = [1, 2, 3];
console.log(arr.includes(4));
function index () {
  console.log(foo);
}

module.exports = index;
```