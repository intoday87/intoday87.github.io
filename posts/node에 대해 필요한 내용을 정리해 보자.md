---
title: node에 대해 필요한 내용을 정리해 보자
---
[`navigator` Object integration](https://nodejs.org/en/blog/announcements/v21-release-announce#navigator-object-integration)
node 21부터 브라우저 환경과의 호환을 위해 global navigator object을 추가했다. 때문에 
기존에 다음과 같이 브라우저 환경을 체크하던 코드들에서 문제가 발생한다

```js
if (navigator !== undefined) {
   // document와 같은 browser 관련된 API 접근
}
```

lottie와 같은 라이브러리에서 문제가 발생하는데 다음과 같은 해결 방법을 제시한다. 
nextjs에서 dynamic import를 사용해  ssr 시점에 import를 제외 하는 방법

```ts
import dynamic from 'next/dynamic'
const Lottie = dynamic(() => import('react-lottie'), { ssr: false })
```
source: https://github.com/Gamote/lottie-react/issues/101#issuecomment-2119614396

node option으로 global navigator를 비활성화 하는 방법
```zsh
NODE_OPTIONS="--no-experimental-global-navigator" next build
```
source: https://github.com/Gamote/lottie-react/issues/101#issuecomment-2456768237
