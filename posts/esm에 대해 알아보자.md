## esm이란?
[JavaScript modules - mdn](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/Modules)을 읽으면서 정맅

브라우저와 달리 node.js에서는 일찍부터 commonjs 기반으로 서버를 기반으로 한 모듈 시스템을 지원하고 있었으나 브라우저는 그렇지 않음

브라우저에서 동작하는 여러 샘플들을 mdn이 [github](https://github.com/mdn/js-examples/tree/main)에 공유
> **주의:** 예제를 다운로드하여 로컬에서 실행하려면, 로컬 웹 서버를 통해 예제를 실행해야 합니다.

javascript module은 두 가지 구문(statement)으로 구성된다
- `export`
	- functions, `var`, `let`, `const`, class를 내보낼 수 있지만 최상위에서 내보내야 한다. 즉 함수내에서 `export` 를 사용할 수 없다
- `import`

`import` 와 `export` 문(statement)은 모듈 내에서만 사용할 수 있다. 정규 스크립트가 아니다
즉 브라우저에서는 `<script type="module" src="./a.js />` 와 같은 형태로 써야 한다. - [mdn](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/Modules#applying_the_module_to_your_html)

##  html에서 사용하는 경우
`type="module"`로 선언해서 가져온다
```html
 <script type="module" src="/scripts/main.js" />
```
main.js에서 import는 경로에 따라 http로 요청해 js 리소스를 가져와 실행한다. file 프로토콜로 사용하는 경우 cors 오류를 마주할 수 있다

###  모듈이 표준 스크립트와 다른 차이 점
- file 프로토콜로 사용시 cors 오류가 발생한다
- 기본적으로 strict 모드
- 야
###  Default export
https://hacks.mozilla.org/2015/08/es6-in-depth-modules 에서 `Default export`를 검색하면 나오는 섹션의 내용.(mdn인데 왜 [location hash](https://developer.mozilla.org/en-US/docs/Web/API/Location/hash)를 왜 안쓰는거지🤔 )
새로운 표준 esm은 CommonJS와 AMD 모듈과 상호 협력하도록 설계 되었다

그 예시로 node 프로젝트에서  `npm install lodash`로 설치한 경우 다음과 같이 가능하다는데 실제로 테스트해보니 되지는 않는다;;

```js
import { each } from 'lodash'

each([1,2,3], console.log)
```

하지만 아래와 같은 코드는 동작한다. CommonJS의 `module.exports = lodash` 와 같은 [코드](https://github.com/lodash/lodash/blob/f299b52f39486275a9e6483b60a410e06520c538/dist/lodash.js#L17201)를 default로 인식해서 import가 가능하다

```js
import { default as _ } from 'lodash' // import _ from 'lodash' 도 가능

_.each([1,2,3], console.log)
```

> All CommonJS and AMD modules are presented to ES6 as having a `default` export, which is the same thing that you would get if you asked `require()` for that module—that is, the `exports` object.

exports의 object를 default로 상호 호환해 인식한다

## `.mjs`에 대해 파헤쳐 보자
- [v8 문서에서 추천하는 이유](https://v8.dev/features/modules#mjs)
	- web에서는 사실 이 확장자는 `Content-type: text/javascript`로 서빙되면 아무 문제가 없다
	- browser에서 `type` 속성으로 부터 script라는 것을 알고 있다
	- 그러므로 2가지 이유로 `.mjs`를 추천한다
		- code를 들여다 보지 않아도 esm module이라고 가정할 수 있다. 즉 시각적으로 누구나 다 전통적인 클래식 스크립트가 아닌 모듈이라고 확신할 수 있다
		- nodejs 및 [d8](https://v8.dev/docs/d8), babel에서 esm module로 구문이 분석되도록 보장한다


package.json에서 [type: "module"](https://nodejs.org/api/packages.html#type)로  선언된 경우 esm only로 해석되어 *.js는 기본적으로 esm으로 해석되게 된다
 js내 export, import 구문 사용 가능
기본적으로 `*.js`는 esm으로 취급한다. `*.mjs`가 아니어도 상관없이)

bundler에서 인식

## Node.js esm에서 확장자를 필수로 요구 한다
https://nodejs.org/api/esm.html#mandatory-file-extensions

```json
//package.json
{
  "name": "import-test-node",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "description": ""
}
```

```js
// index.js
import a from './a'
```

다음과 같이 오류 발생
```zsh
node:internal/modules/run_main:129
    triggerUncaughtException(
    ^

Error [ERR_MODULE_NOT_FOUND]: Cannot find module '/Users/USER/vscode-repo/temp/import-test-node/a' imported from /Users/USER/vscode-repo/temp/import-test-node/import.js
Did you mean to import "./a.js"?
    at finalizeResolution (node:internal/modules/esm/resolve:265:11)
    at moduleResolve (node:internal/modules/esm/resolve:933:10)
    at defaultResolve (node:internal/modules/esm/resolve:1169:11)
    at ModuleLoader.defaultResolve (node:internal/modules/esm/loader:383:12)
    at ModuleLoader.resolve (node:internal/modules/esm/loader:352:25)
    at ModuleLoader.getModuleJob (node:internal/modules/esm/loader:227:38)
    at ModuleWrap.<anonymous> (node:internal/modules/esm/module_job:87:39)
    at link (node:internal/modules/esm/module_job:86:36) {
  code: 'ERR_MODULE_NOT_FOUND',
  url: 'file:///Users/USER/vscode-repo/temp/import-test-node/a'
}
```

## todo esm과 cjs의 로딩 방식
- esm: 비동기
- cjs: 동기

## todo commonjs 와의 차이
- 복사냐 참조냐

#esm #javascript-module