---
title: jest cookbook
---
## spy
일부만 mocking할 때 쓴다. 전체를 다 mock으로 구현하기에는 복잡한 객체의 일부만 구현해서 사용할 때 쓰고 있다.

`Cookies`
```ts
import Cookies from 'cookies'


describe('test', () => {
    test('test something', () => {
        const req = {} as jest.Mocked<IncomingMessage>
        const res = {} as jest.Mocked<ServerResponse>
        const cookies = new Cookies(req, res)
        const headers = {} as jest.Mocked<IncomingHttpHeaders
    })
})
```