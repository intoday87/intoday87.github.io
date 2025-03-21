---
title: react 19 `use`에 대해 정리해 보자
---
react 19에서 `use`는 서버 컴포넌트와 클라이언트 컴포넌트에서 같은 방식으로 비동기를 처리할 수 있도록 인터페이스를 제공하고자 한다. `use`는 아마도 최초이자 마지막으로 조건부에서도 호출 가능한 hook이 될 것이라고 하고 `promise` 및 `context`를 인자로 받는다. 이름 그대로 hook이기 때문에 react component 트리 내에서 사용되는 훅이다.
context의 경우는 공부를 해봐야 알겠지만 `promise`를 받는 경우 `async` & `await`없이 비동기 함수를 처리해준다. next 15 기준으로 작성된 다음과 같은 코드를 보자. 

```tsx
// app/page.tsx 

import Message from "@/components/Message";
import { Suspense } from "react";

function fetchMessage() {
  return new Promise<string>((resolve) => {
    setTimeout(() => resolve("🚀 서버에서 받은 데이터!"), 2000);
  });
}

export default function Home() {
  return (
    <div>
      <Suspense fallback={<div>loading...</div>}>
          <Message messagePromise={fetchMessage()} />
      </Suspense>
    </div>
  );
}

```

```tsx
// components/Message.tsx
'use client';

import { use } from "react";

export default function Message({
  messagePromise,
}: {
  messagePromise: Promise<string>;
}) {
  const message = use(messagePromise);
  return <div>{message}</div>;
}

```

위의 코드는 정확히 동작한다. ssr에서 `Suspense`의 `fallback`이 html로 렌더링되어 내려오고 cient에서 `promise`를 resolve해서 결과를 가져와 보여준다. `fetchMessage`는 어디에 위치하고 있을까?
`🚀 서버에서 받은 데이터!`가 어디에 위치를 하고 있는지 찾아봤다. document로 내려온 소스는 다음 같다

```html
    <body class="geist_e531dabc-module__QGiZLq__variable geist_mono_68a01160-module__YLcDdW__variable">
        <div>
            <!--$?-->
            <template id="B:0"></template>
            <div>loading...</div>
            <!--/$-->
        </div>
        <script src="/_next/static/chunks/node_modules_next_dist_compiled_react-dom_1f56dc._.js" async=""></script>
        <script>
            (self.__next_f = self.__next_f || []).push([0])
        </script>
		<!-- 다른 self.__next_f.push 들이 사이에... -->
        <script>
            self.__next_f.push([1, "f:\"🚀 서버에서 받은 데이터!\"\n"])
        </script>
        <div hidden id="S:0">
            <div>🚀 서버에서 받은 데이터!</div>
        </div>
```

위 결과를 보면 별도로 서버에 요청해서 가져오는 [streaming](https://react.dev/reference/react/use#streaming-data-from-server-to-client) 방식일 줄 알고 xhr 요청을 기대했으나 fallback 및 `promise`의  resolve된 결과가 이미 포함되어 내려오고 `fetchMessage` 함수 자체는 보이지 않으나 실제 `setTimeout`으로 기다리는 시간만큼 실제로 `promise`를 이행하는 코드가 실행되는 것으로 보인다.

`Suspense`로 감싸져 있는 경우와 아닌 경우도 다르다.
감싸져 있지 않은 경우 기존의 ssr 동작과 같이 html에 이미 서버에서 resolve된 결과가 렌더링 되어 응답으로 날아온다. 즉 바로 `fallback` 없이 바로 결과가 보인다.

```html
<body class="geist_e531dabc-module__QGiZLq__variable geist_mono_68a01160-module__YLcDdW__variable">
        <div>
            <div>🚀 서버에서 받은 데이터!</div>
        </div>
		<!-- 다른 self.__next_f.push 들이 사이에... -->
        <script>
            (self.__next_f = self.__next_f || []).push([0])
        </script>
        <script>
            self.__next_f.push([1, "e:\"🚀 서버에서 받은 데이터!\"\n"])
        </script>
    </body>

```