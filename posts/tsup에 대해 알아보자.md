[state of js 2023 build tools](https://2023.stateofjs.com/en-US/libraries/build_tools/)에 보면 이 녀석이 등장했다. 시작과 꽤 좋은 반향을 일으킨 것 같다

docs에 보니 bundler인 것 같고 이렇게 적혀 있다

> What can it bundle
> Bundle your TypeScript library with no config, powered by [esbuild](https://github.com/evanw/esbuild).

ts를 별도의 plugin
설치해서 실행해 보자

```zsh
pnpm init
pnpm add tsup -D
```

파일 생성해서 간단한 코드 생성

src/index.ts
```ts
const a: number = 10
console.log(a)
```

실행해보자
```
pnpm tsup src/index.ts

Error: Cannot find module 'typescript'
Require stack:
- /Users/USER/vscode-repo/temp/tsup-practice/node_modules/.pnpm/tsup@8.3.0/node_modules/tsup/dist/index.js
- /Users/USER/vscode-repo/temp/tsup-practice/node_modules/.pnpm/tsup@8.3.0/node_modules/tsup/dist/chunk-JNR3R42T.js
- /Users/USER/vscode-repo/temp/tsup-practice/node_modules/.pnpm/tsup@8.3.0/node_modules/tsup/dist/cli-default.js
    at Module._resolveFilename (node:internal/modules/cjs/loader:1145:15)
    at Module._load (node:internal/modules/cjs/loader:986:27)
    at Module.require (node:internal/modules/cjs/loader:1233:19)
    at require (node:internal/modules/helpers:179:18)
    at Object.<anonymous> (/Users/USER/vscode-repo/temp/tsup-practice/node_modules/.pnpm/tsup@8.3.0/node_modules/tsup/dist/index.js:1021:19)
    at Module._compile (node:internal/modules/cjs/loader:1358:14)
    at Module._extensions..js (node:internal/modules/cjs/loader:1416:10)
    at Module.load (node:internal/modules/cjs/loader:1208:32)
    at Module._load (node:internal/modules/cjs/loader:1024:12)
    at Module.require (node:internal/modules/cjs/loader:1233:19)
```

typescript를 찾는다

```zsh
pnpm add typescript -D
pnpm tsup src/index.ts # 2t
CLI Building entry: src/index.ts
CLI tsup v8.3.0
CLI Target: node16
CJS Build start
CJS dist/index.js 44.00 B
CJS ⚡️ Build success in 411ms
```

성공한다. 설정 없이 바로 bundle로 만들었다
dist/index.js를 봐보자

```js
// src/index.ts
var a = 10;
console.log(a);
```

문서를 읽다보니 bundling시 [Excluding packages](https://github.com/egoist/tsup/tree/main/docs#excluding-packages) 를 보니까 dependencies와 peerDependencies를 제외 한다고 되어 있다. 테스트를 해보자

```
pnpm add hello-world-npm
```

import를 해서 entry point에 추가해 보자

```
```