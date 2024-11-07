---
title: rollup에서 browserslist를 인식해서 polyfill이 포함되도록 해보자
---
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
			  // ie 9부터 지원 Array.prototype.includes를 지원하지 않는다
              browsers: ['ie >= 9'],
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

## @babel/preset-env

### `useBuiltIns`
polyfill을 핸들링하는 방법을 지원하는 옵션. `entry`, `usage`를 사용하면 core-js를 직접 import 또는 require를 추가한다. core-js 파일 그 자체로 결정되서 추가되며 접근 가능해야 한다.
[package.json](https://github.com/babel/babel/blob/438b24cabca5715e311af1e3c53bc728cfbdf04d/packages/babel-preset-env/package.json#L86) 을 보면 core-js를 가지고 있지 않고 `core-js-compat`만 가지고 있다.  즉 [`corejs`](https://babeljs.io/docs/babel-preset-env#corejs) 옵션에서 버전을 지정할 경우 설치된 core-js의 버전 스펙을 그대로 따라가는것이 아니라 [core-js-compat](https://github.com/zloirock/core-js/blob/v3.39.0/packages/core-js-compat/src/modules-by-versions.mjs)에서 옵션에 지정된 매핑을 찾아서 3.0 이후로 그 버전에 추가된 
- entry
- usage
- false

## `.browserslist` 파일은 그럼 필요한가?
rollup.config.js에서 설정하는데 별도로 `.browserslist`이 필요 한가?

https://github.com/browserslist/browserslist#queries를 보면 browserslist를 조회하는 순서에 대해 설명하고 있다

> 1. `.browserslistrc` config file in current or parent directories.
> 2. `browserslist` key in `package.json` file in current or parent directories.
> 3. `browserslist` config file in current or parent directories.
> 4. `BROWSERSLIST` environment variable.
> 5. If the above methods did not produce a valid result Browserslist will use defaults: `> 0.5%, last 2 versions, Firefox ESR, not dead`.


babel-preset-env [Browserslist Integration](https://babeljs.io/docs/babel-preset-env#browserslist-integration)에서 설명하고 있는 부분을 보자
> For browser- or Electron-based projects, we recommend using a [`.browserslistrc`](https://github.com/browserslist/browserslist) file to specify targets.

babel-preset-env에서 .browserslistrc를 인식한다고 되어 있다
```json
// babel.config.json
{  
	"presets": [  
		[  
			"@babel/preset-env",  
			{  
				"useBuiltIns": "usage",  
				"corejs": "3.22"  
			}  
		]  
	]  
}
```

```
// .browserslistrc
ie >= 9
```

실제로 테스트를 해보니 `require('core-js/modules/es.array.includes.js');`가 포함된다

```js
'use strict';

require('core-js/modules/es.array.includes.js');

var foo = 'hello world!';

var arr = [1, 2, 3];
console.log(arr.includes(4));
function index () {
  console.log(foo);
}

module.exports = index;
```

### useBuiltIns
## @babel/preset-env를 사용하는 이유?
### [browserslist-integration](https://babeljs.io/docs/babel-preset-env#browserslist-integration)
.browserslist 파일을 인식해서 별도로 `target` 속성에 명시해줄 필요가 없다
```json
//babel.config.json
{  
	"presets": [  
		[  
			"@babel/preset-env",  
			{  
				// target 속성이 없음을 주목
				"useBuiltIns": "entry",  
				"corejs": "3.22"  
			}  
		]  
	]  
}
```

```
// .browserslist
> 0.25%  
not dead
```

### @babel/preset-env를 사용하지 않고 browserslist를 통합해서 사용할 수 없나?