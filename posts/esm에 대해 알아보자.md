esm이란?

commonjs와의 차이
- 복사냐 참조냐

package.json에서 [type: "module"](https://nodejs.org/api/packages.html#type)로  선언된 경우 esm only로 해석되어 *.js는 기본적으로 esm으로 해석되게 된다
- js내 export, import 구문 사용 가능

bundler에서 인식

node esm에서 확장자를 필수로 요구
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

e