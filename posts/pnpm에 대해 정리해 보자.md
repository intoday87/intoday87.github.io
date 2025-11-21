---
title: pnpm에 대해 정리해 보자
---
## 설치하지 않고 로컬에 있는 프로젝트 연결 [`pnpm link <dir>`](https://pnpm.io/ko/cli/link)
기존 npm node_modules 파일의 경로를 확인해보면 node_modules/.pnpm 하위 설치된 파일들로 링크되어 있다

현재 프로젝트에 node_modules에 링크 된다
`lrwxr-xr-x@ 1 USER  staff    38B 10 29 20:09 @nick/a -> ../../../nick/packages/a`
디렉토리 자체를 링크 시켜서 dist 말고도 소스 다 가져옴
-  [ ] TODO `peerDependencies`를 못찾는 문제는 어떻게 해결?

 file protocol로 상대 경로로 연결 후  install해서 설치 하는 방법
`pnpm link <dir>` 과 다르게 dist만 가져옴
	```json
	        "@nick/a": "file:../nick/packages/a",
	```

## [pnpm dedupe](https://pnpm.io/cli/dedupe)

> Perform an install removing older dependencies in the lockfile if a newer version can be used.

캐럿(`^`)이나 tilde(`~`)로 변경 가능한 오래된 디펜던시를 갱신해주고 사용되지 않는 디펜던시도 제거가 되는것으로 보인다

## How peers are resolved
peer dependency가 없는 경우 하나의 dependency는 하나의 dependency


## workspace

### workspace 참조
- pnpm-workspace.yaml에 packages로 명시된 worksp