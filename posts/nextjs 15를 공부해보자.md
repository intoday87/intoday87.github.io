---
title: nextjs 15를 공부해보자
---
# [Loading UI and Streaming](https://nextjs.org/docs/app/building-your-application/routing/loading-ui-and-streaming)
### 학습 목표
- [ ] 기존 ssr에서 gssp의 모든 요청을 기다리는 대신 server suspense를 사용하여 개별 streaming이 가능한가?
일단은 해보니까 streaming이 되는것 같다. 간단한 예시를 만들어 보자

```tsx
// app/page.tsx

import Message from "@/components/Message";
import { Suspense } from "react";

function fetchMessage() {
  return new Promise<string>((resolve) => {
    setTimeout(() => resolve("🚀 서버에서 받은 데이터!"), 7000); // 응답시간 조절
  });
}

export default function Home() {
  return (
    <div>
      {/* 주석을 풀면 loading.tsx를 사용하지 않고 감싼 Suspense가 promise를 처리하게 된다 */}
      {/* <Suspense fallback={<div>loading...?</div>}> */}
      <Message messagePromise={fetchMessage()} />
      {/* </Suspense> */}
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

// export default async function Message({
//   messagePromise,
// }: {
//   messagePromise: Promise<string>;
// }) {
//   const message = await messagePromise;
//   return <div>받은 메세지: {message}</div>;
// }
```

신기하게 `Message` 컴포넌트가 client 컴포넌트건 server 컴포넌트건 결과가 같은점이 

- [ ] latency를 위해 정해놓은 timeout이 넘어가면 client는 렌더링을 시작하고 timeout이 넘은 컴포넌트는 streaming처리로 suspense와 함께 skeleton과 같은 로딩 스테이트를 보여주면서 별도로 요청을 기다릴 수 있는가?