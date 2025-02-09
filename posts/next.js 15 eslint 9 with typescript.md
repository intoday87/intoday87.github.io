---
title: next.js 15에서 eslint 9의 설정파일을 typescript로 쓰기
---
next.js 15에서 eslint 9를 사용하면서 eslint config file도 typescript를 지원하게 될 줄 알았는데 아직 안되고 있었다. eslint.config.mts로 이름을 변경하고 다음과 같이 타입을 주었는데 `next lint`에서 lint 파일을 인식을 못한다

```ts
// eslint.config.mts
import { dirname } from "path";
import { fileURLToPath } from "url";
import { FlatCompat } from "@eslint/eslintrc";
import { Linter } from "eslint";

const __filename = fileURLToPath(import.meta.url);
const __dirname = dirname(__filename);

const compat = new FlatCompat({
  baseDirectory: __dirname,
});

const eslintConfig: Linter.Config[] = [ // lint type 추가
  ...compat.extends("next/core-web-vitals", "next/typescript"),
];

export default eslintConfig;
```

package.json에서는 분명 9.20.0의 eslint를 사용하고 있다
```zsh
pnpm ls eslint version
Legend: production dependency, optional only, dev only

nextjs-15-practice@0.1.0 /Users/nick/vscode-repos/nextjs-15-practice (PRIVATE)
```

찾아보니 다음과 같은 이슈가 올라와 있다. [# next lint does not recognize eslint.config.mts #74900](https://github.com/vercel/next.js/issues/74900)
확인했던대로 eslint 9에서는 [TypeScript Configuration Files](https://eslint.org/docs/latest/use/configure/configuration-files#typescript-configuration-files)과 같이 deno나 bun에서는 ts를 별도 설정 없이 인지하지만 nodejs환경에서는 [jiti](https://github.com/unjs/jiti)를 설치해서 지원한다고 되어 있는데 막상 `next lint`를 호출해 보면 위의 이슈 내용대로 설정 파일을 찾지 못한다.

```zsh
$ next lint
(node:583585) ExperimentalWarning: CommonJS module /home/imbios/projects/dashboardthing-example/next.config.compiled.js is loading ES Module /home/imbios/projects/dashboardthing-example/src/env.js using require().
Support for loading ES Module in require() is an experimental feature and might change at any time
(Use `node --trace-warnings ...` to show where the warning was created)
? How would you like to configure ESLint? https://nextjs.org/docs/app/api-reference/config/eslint
❯  Strict (recommended)
   Base
   Cancel
```

원인은 이 [comment](https://github.com/vercel/next.js/issues/74900#issuecomment-2604725409)대로 (m|c)ts 파일을 주석처리를 해놔서 지원이 안되고 있었다. 그리고 이 주석을 해제하는 [PR](https://github.com/vercel/next.js/pull/75222)이 올라와있고 확인해보면  [`f10073b`](https://github.com/vercel/next.js/commit/f10073be5943c04e8e12b2908b9c61c028b28df0)으로 머지되었고 [v15.2.0-canary.47](https://github.com/vercel/next.js/releases/tag/v15.2.0-canary.47)에 반영 되었다. 해당 버전으로 next를 설치해서 띄우고 jiti를 설치한다음 eslint config를 mts로 변경해서 사용하니 lint가 동작한다.