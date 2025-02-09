---
title: next.js 15 eslint 9 with typescript
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

찾아보니 다음과 같은 이슈가 올라와 있다. [# next lint does not recognize eslint.config.mts #74900](https://github.com/vercel/next.js/issues/74900#issuecomment-2604725409)
확인했던대로 eslint 9에서는 [TypeScript Configuration Files](https://eslint.org/docs/latest/use/configure/configuration-files#typescript-configuration-files)](https://eslint.org/docs/latest/use/configure/configuration-files#typescript-configuration-files)과 같이 deno나 bun에서는 ts를 인지하지만 nodejs환경에서는 [https://github.com/unjs/jiti]