## [Concepts](https://trpc.io/docs/concepts)

일단 concepts 페이지 내용이 단순해서 좋다
### It's just functions

trpc는 T(ypescript) & Remote Procedure Call이다
rpc하면 보통 백엔드에서 말 그대로 원격 함수 호출인데 이 개념을 front-end 개발에 들고온 것이고 클라이언트에서 서버(\*)로의 api 요청을 원격 함수 호출의 형태로 단순화 하고 서버의 API 요청 및 응답에 필요한 타입 지원을 typescript로 지원 받도록 하는 것으로 이해된다

\*: 우리는 서버와 클라이언트 모델을 주로 사용하지만 rpc 개념에서는 서버라기 보단 다른 원격지 호스트를 의미

다음과 같은 한 문장이 잘 설명해주고 있는듯 하다

> tRPC (TypeScript Remote Procedure Call) is one implementation of RPC, designed for TypeScript monorepos. It has its own flavor, but is RPC at its heart.

### Don't think about HTTP/REST implementation details

trpc의 추상화 레벨의 구현에는 신경쓰지 말라는 내용이 담겨 있다. 어떤 레이어의 통신이든 신경쓰지 않고 일관화된 trpc의 함수 호출을 통해 통신할 것이다라는 내용. 추상화가 매우 잘 되어 있고 trpc를 믿어라 라고 말하는 듯한 내용. 추상화에 대한 내용에 맞게 함수의 이름에 집중 하라는 메세지가 좋다.

`getUser(id)`(trpc) -> `GET /users/:id`(REST)

반드시 REST의 표현 방식대로 http 통신의 엔드 포인트를 상상하면서 함수 이름을 매칭하라는 강박적인 내용은 아니고 우리는 우리가 필요한 행동(method)에 집중해서 함수의 이름을 짓고 그 함수를 사용한다

##  trpc vs server actions

요즘 어떻게 사용하나 하고 정찰하다가 reddit의 글을 봤다
[reddit - # tRPC vs Server Actions ](https://www.reddit.com/r/nextjs/?f=flair_name%3A%22Discussion%22)
요지는 server action은 설계상 mutation을 위해 설계됬고 client에서 병렬로 요청하더라도 순차적으로 처리되기 때문에 데이터 페칭에 적합하지 않다
[nextjs에서 server action은 순차적으로 실행된다?](https://intoday87.github.io/posts/nextjs%EC%97%90%EC%84%9C%20server%20action%EC%9D%80%20%EC%88%9C%EC%B0%A8%EC%A0%81%EC%9C%BC%EB%A1%9C%20%EC%8B%A4%ED%96%89%EB%90%9C%EB%8B%A4)









