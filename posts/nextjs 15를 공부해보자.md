---
title: nextjs 15를 공부해보자
---
# [Loading UI and Streaming](https://nextjs.org/docs/app/building-your-application/routing/loading-ui-and-streaming)
### 학습 목표
- [ ] 기존 ssr에서 gssp의 모든 요청을 기다리는 대신 server suspense를 사용하여 개별 streaming이 가능한가?
일단은 해보니까 streaming이 되는것 같다🕵🏽 간단한 예시를 만들어 보자!

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
		<!-- script self.__next_f.push 의 반복. 아직 얘네들 이 부분 뭐하는건지 모름-->
		<!-- 스트리밍이 된다는 걸 알았던 이유가 이 부분에서 fetchMessage의 7초를 기다리고 있다. 즉 다음 라인은 없고 7초가 지난 후 스트리밍된 결과가 다음 라인부터 붙는다. 와우🤩 -->
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
			/** react 18의 서버 컴포넌트 및 스트리밍 렌더링과 관련된 클라이언트 측 복구 로직 하나로 보인다
			* @param b 업데이트할 노드(플레이스홀더)의 ID
			* @param c 제거할 노드의 ID
			* @param e? data-digest 검증용 해시값인듯?
			*/
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
`$RC` 함수를 통해 hidden으로 감춰진 스트리밍된 결과값을 client 복구 함수를 통해서 복구한다.
`template` 엘리먼트트의 id가 `B:0`인데 여기에 `S:0`이 id인 내용을 넣는다. 그리고 `template`아래 suspense fallback이 별도의 id가 없는데도 없애준다.

#### streaming marker <!--$--><!--/$-->
suspense fallback이 별도의 id를 가지지 않았는데 처리가 되는점은 확인해보니 streaming marker 주석을 활용해서 처리 한다
#### text/html은 어떻게 streaming을 해오는것인가
 대기중인 promise가 처리될 때까지 streaming write를 통해서 완료된 html 조각을 응답 스트리밍에 쓰는것으로 보인다.  별도의 스트리밍 컨텐트 타입 헤더가 없이 동작하는게 신기하다. 원래 가능했나..?
#### 통짜 hydration 대신 이제 병렬처리니까 컴포넌트마다 streaming이 가능하다는 것인데?
시도해보자!  다른 한 컴포넌트는 11초를 기다리도록 하고 메세지를 간단히 바꿔보자. 위에서 생각했던대로 streaming write를 promise가 완료된 순서대로 write 하고 있음을 응답을 보면 알 수 있다
```tsx
// app/page.tsx

import Message from "@/components/Message";
import { Suspense } from "react";

function fetchMessage() {
  return new Promise<string>((resolve) => {
    setTimeout(() => resolve("🚀 서버에서 받은 데이터! - messsage 1"), 7000);
  });
}

function fetchMessage2() {
  return new Promise<string>((resolve) => {
    setTimeout(() => resolve("🚀 서버에서 받은 데이터! - messsage 2"), 11000);
  });
}

export default function Home() {
  return (
    <div>
      {/* 주석을 풀면 loading.tsx를 사용하지 않고 감싼 Suspense가 promise를 처리하게 된다 */}
      {/* <Suspense fallback={<div>loading...?</div>}> */}
      <Message messagePromise={fetchMessage()} />
	  <Message messagePromise={fetchMessage2()} />
      {/* </Suspense> */}
    </div>
  );
}
```

한 가지 알게된 점은 page와 같은 directory내 loading.tsx를 만들어 놓으면 모든 서버의 요청을 다 기다리고 난 후에 스트리밍된 결과를 받으면 사라진다. 즉 개별 서버 컴포넌트를 suspense로 따로 감싸 주지 않으면 스트리밍하는 이유가 없네?!

```tsx
// 상위 코드는 기존과 같다

export default function Home() {
  return (
    <div>
      <Suspense fallback={<div>loading waitting 1...?</div>}> 
	      <Message messagePromise={fetchMessage()} />
	  </Suspense>
      <Suspense fallback={<div>loading waitting 2...?</div>}> 
		  <Message messagePromise={fetchMessage2()} />
	  </Suspense>
    </div>
  );
}
```

결과는
```txt
🚀 서버에서 받은 데이터! - messsage 0
loading waitting 1...?
```

- [ ] latency를 위해 정해놓은 timeout이 넘어가면 client는 렌더링을 시작하고 timeout이 넘은 컴포넌트는 streaming처리로 suspense와 함께 skeleton과 같은 로딩 스테이트를 보여주면서 별도로 요청을 기다릴 수 있는가?