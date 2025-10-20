server action은 함수 호출이라 병렬로 처리가 가능할 것 같지만 react re-rendering 프로세스(transition)을 발생시키기 때문에 순차적으로 응답이 처리되는것으로 보인다.

https://react.dev/reference/rsc/use-server#caveats 에 중요한 설명이 있다

> - Server Functions are designed for mutations that update server-side state; they are not recommended for data fetching. Accordingly, frameworks implementing Server Functions typically process one action at a time and do not have a way to cache the return value.

한 번에 하나의 action을 처리하도록 구현되어 있는것으로 보인다

> - Server Functions should be called in a [Transition](https://react.dev/reference/react/useTransition). Server Functions passed to [`<form action>`](https://react.dev/reference/react-dom/components/form#props) or [`formAction`](https://react.dev/reference/react-dom/components/input#props) will automatically be called in a transition.

server action은 form에서 호출할 때 transition을 유발한다