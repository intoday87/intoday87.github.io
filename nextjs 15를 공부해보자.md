---
title: nextjs 15를 공부해보자
---
# [Loading UI and Streaming](https://nextjs.org/docs/app/building-your-application/routing/loading-ui-and-streaming)
### 학습 목표
- [ ] 기존 ssr에서 gssp의 모든 요청을 기다리는 대신 server suspense를 사용하여 개별 streaming이 가능한가?
- [ ] latency를 위해 정해놓은 timeout이 넘어가면 client는 렌더링을 시작하고 timeout이 넘은 컴포넌트는 streaming처리로 suspense와 함께 skeleton과 같은 로딩 스테이트를 보여주면서 별도로 요청을 기다릴 수 있는가?