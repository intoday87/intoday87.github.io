---
title: peerDependencies에 대해 정리해 보자
---
## peerDependencies란 무엇인가?

source: [nodejs npm peerDependencies blog](https://nodejs.org/en/blog/npm/peer-dependencies )의 내용을 정리해 보자
###  The Problem: plugin
플러그인의 경우 대부분 host package의 의존성을 사용하지 않지만 host package에 의존하는 경우에 해당한다. 이런 경우 