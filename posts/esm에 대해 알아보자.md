## esm이란?
[JavaScript modules - mdn](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/Modules)을 읽고 남겨보자

브라우저와 달리 node.js에서는 일찍부터 commonjs 기반으로 서버를 기반으로 한 모듈 시스템을 지원하고 있었으나 브라우저는 그렇지 않음

javascript module은 두 가지 구문(statement)으로 구성된다
- export
- import

사용법은 위 mdn 링크를 참고하자

`import` 와 `export` 문(statement)은 모듈 내에서만 사용할 수 있다. 정규 스크립트가 아니다





## commonjs와의 차이
- 복사냐 참조냐

package.json에서 [type: "module"](https://nodejs.org/api/packages.html#type)로  선언된 경우 esm only로 해석되어 *.js는 기본적으로 esm으로 해석되게 된다
- js내 export, import 구문 사용 가능

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
//
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