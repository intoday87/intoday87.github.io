---
title: react 19 use에 대해 정리해 보자
---
react 19에서 `use`는 서버 컴포넌트와 클라이언트 컴포넌트에서 같은 방식으로 promise를 처리할 수 있도록 인터페이스를 제공하고자 한다. `use`는 아마도 최초이자 마지막으로 조건부에서도 호출 가능한 hook이 될 것이라고 하고 `promise` 및 `context`를 인자로 받는다. 이름 그대로 hook이기 때문에 react component 트리 내에서 사용되는 훅이다.
context의 경우는 공부를 해봐야 알겠지만 `promise`를 받는 경우 `async` & `await`없이 비동기 함수를 처리해준다. 다음과 같은 코드를 보자

```

```

