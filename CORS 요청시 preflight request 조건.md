브라우저에서 CORS 요청시 [단순 요청](https://developer.mozilla.org/ko/docs/Web/HTTP/Guides/CORS#%EB%8B%A8%EC%88%9C_%EC%9A%94%EC%B2%ADsimple_requests)이 아니면 [사전 요청(preflight request)](https://developer.mozilla.org/ko/docs/Glossary/Preflight_request)이 발생한다. 

## 사전 요청

CORS 요청시 단순 요청이 아니면 http 메소드를 `OPTIONS`로 해서 실제로 보낼려는 http 요청에 앞서 해당 요청이 처리 가능한지 먼저 요청을 보내서 확인을 받는다

```js
// 단순 요청으로 처리되지 않는 경우 preflight 요청을 보내는 경우
fetch('https://jsonplaceholder.typicode.com/posts/1', { headers: { 'cache-control': 'no-cache' } })
```

다음은 preflight 요청이 발생한 경우 네트워크 탭 정보이다

```
// network tab

Request URL https://jsonplaceholder.typicode.com/posts/1
Request Method OPTIONS
Status Code 204 No Content
Remote Address xx.xx.xx.x:443
Referrer Policy strict-origin-when-cross-origin

// response header 일부
access-control-allow-credentials true
access-control-allow-headers cache-control
access-control-allow-methods GET,HEAD,PUT,PATCH,POST,DELETE
access-control-allow-origin https://some.domain.com

// request header 일부
access-control-request-headers cache-control
access-control-request-method GET
```

`access-control-request-method`로 `GET` 요청에 대한 허용을 응답 헤더로 받는 부분과 `access-control-request-headers`로 `cache-control`을 받을수 있을지도 요청과 응답헤더를 보면 확인할 수 있다

단순 요청에서 `Accept`를 제외한 대부분의 헤더는 단순 요청에 해당하지 않기 때문에 preflight 요청은 생략되지 않았으며 허용된 응답 후에 비로소 실제 `GET` 요청을 보내게 된다