---
title: react-query에 대해 정리해 보자
---
## `isLoading`과 `isFetching`의 차이
한 번 까먹어서 정리해 놓는다
- `isLoading`: 캐시에 데이터 조차 없을때 최초 로딩하는 경우
- `isFetching`: `staleTime` 정책에 따라 stale데이터인 경우 fresh한 데이터를 요청하기 위해 재요청중인 경우