---
title: pnpm에 대해 정리해 보자
---
### 설치하지 않고 로컬에 있는 프로젝트 연결 [`pnpm link <dir>`](https://pnpm.io/ko/cli/link)
현재 프로젝트에 node_modules에 링크 된다
`lrwxr-xr-x@ 1 USER  staff    38B 10 29 20:09 @nick/a -> ../../../nick/packages/a`
- TODO `peerDependencies`를 못찾는 문제는 어떻게 해결?