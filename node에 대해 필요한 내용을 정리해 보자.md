---
title: node에 대해 필요한 내용을 정리해 보자
---
[`navigator` Object integration](https://nodejs.org/en/blog/announcements/v21-release-announce#navigator-object-integration)
node 21부터 브라우저 환경과의 호환을 위해 global navigator object을 추가했다. 때문에 
기존에 다음과 같이 브라우저 환경을 체크하던 코드들에서 문제가 발생한다.
```js
if (na
```