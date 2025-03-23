---
title: nextjs 15를 공부해보자
---
# [Loading UI and Streaming](https://nextjs.org/docs/app/building-your-application/routing/loading-ui-and-streaming)
### 학습 목표
- [ ] 기존 ssr에서 gssp의 모든 요청을 기다리는 대신 server suspense를 사용하여 개별 streaming이 가능한가?
일단은 해보니까 streaming이 되는것 같다 간단한 예시를 만들어 보자

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

신기하게 `Message` 컴포넌트가 client 컴포넌트건 server 컴포넌트건 결과가 같은점이 흥미롭다.
streaming이 된다고 확인한 것은 document 응답을 보면 알 수가 있다

```html
<!DOCTYPE html>
<html lang="en">
    <head>
    <!-- head 내용 생략 -->
    </head>
    <body>
	    <!--$?-->
        <template id="B:0"></template>
        <div>
            <p>loading...!!</p>
        </div>
        <!--/$-->
        <script src="/_next/static/chunks/%5Bturbopack%5D_browser_dev_hmr-client_hmr-client_ts_851d2d63._.js" async=""></script>
        <div hidden id="S:0">
            <div>
                <template id="P:1"></template>
            </div>
            <!--$-->
            <!--/$-->
            <!--$-->
            <!--/$-->
        </div>
        <script>
            (self.__next_f = self.__next_f || []).push([0])
        </script>
		<!-- script self.__next_f.push 의 반복. 스트리밍이 된다는 걸 알았던 이유가 이 부분에서 fetchMessage의 7초를 기다리고 있다. 7초가 지난 후 스트리밍된 결과가 다음 라인부터 붙는다 -->
		 <div hidden id="S:1">
            <div>🚀 서버에서 받은 데이터!</div>
        </div>
        <script>
            $RS = function(a, b) {
                a = document.getElementById(a);
                b = document.getElementById(b);
                for (a.parentNode.removeChild(a); a.firstChild; )
                    b.parentNode.insertBefore(a.firstChild, b);
                b.parentNode.removeChild(b)
            }
            ;
            $RS("S:1", "P:1")
        </script>
        <script>
            $RC = function(b, c, e) {
                c = document.getElementById(c);
                c.parentNode.removeChild(c);
                var a = document.getElementById(b);
                if (a) {
                    b = a.previousSibling;
                    if (e)
                        b.data = "$!",
                        a.setAttribute("data-dgst", e);
                    else {
                        e = b.parentNode;
                        a = b.nextSibling;
                        var f = 0;
                        do {
                            if (a && 8 === a.nodeType) {
                                var d = a.data;
                                if ("/$" === d)
                                    if (0 === f)
                                        break;
                                    else
                                        f--;
                                else
                                    "$" !== d && "$?" !== d && "$!" !== d || f++
                            }
                            d = a.nextSibling;
                            e.removeChild(a);
                            a = d
                        } while (a);
                        for (; c.firstChild; )
                            e.insertBefore(c.firstChild, a);
                        b.data = "$"
                    }
                    b._reactRetry && b._reactRetry()
                }
            }
            ;
            $RC("B:0", "S:0")
        </script>
    </body>
</html>
```
와.. 신기하다! 응답 헤더의 `Content-Type`은 그냥 text/html인데..

- [ ] latency를 위해 정해놓은 timeout이 넘어가면 client는 렌더링을 시작하고 timeout이 넘은 컴포넌트는 streaming처리로 suspense와 함께 skeleton과 같은 로딩 스테이트를 보여주면서 별도로 요청을 기다릴 수 있는가?