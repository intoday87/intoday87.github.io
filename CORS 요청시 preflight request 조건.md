브라우저에서 CORS 요청시 [단순 요청](https://developer.mozilla.org/ko/docs/Web/HTTP/Guides/CORS#%EB%8B%A8%EC%88%9C_%EC%9A%94%EC%B2%ADsimple_requests)이 아니면 [사전 요청(preflight request)](https://developer.mozilla.org/ko/docs/Glossary/Preflight_request)이 발생한다. 

## 사전 요청

CORS 요청시 단순 요청이 아니면 http 메소드를 `OPTIONS`로 해서 실제로 보낼려는 http 요청에 앞서 해당 요청이 처리 가능한지 먼저 요청을 보내서 확인을 받는다

```js
// 단순 요청으로 처리되지 않는 경우 preflight 요청을 보내는 경우
fetch('https://jsonplaceholder.typicode.com/posts/1', { headers: { 'cache-control': 'no-cache' } })
```

```
// network tab

Request URL https://jsonplaceholder.typicode.com/posts/1
Request Method OPTIONS
Status Code 204 No Content
Remote Address xx.xx.xx.x:443
Referrer Policy strict-origin-when-cross-origin


//
```