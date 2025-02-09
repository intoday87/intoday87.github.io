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

package.json에서는 