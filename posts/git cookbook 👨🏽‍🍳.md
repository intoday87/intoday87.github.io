---
title: git cookbook 👨🏽‍🍳
---
가끔 diff or stage에 남아있는 신경 안써야할 변경사항 때문에 커밋이나 처리시 git 인식을 하는(git hook이나 등등) 변경사항을 stash 하라고 하지만 커밋은 하면 안되고 변경사항은 남아 있는 채로 작업을 해야할 때가 있다.
`pnpm publish`와 같은
`pnpm publish`시 `--no-git-checks` 옵션을 사용할 수 있다 가이드 하기도 하지만 변경사항 자체를 git 변경사항에서 신경쓰고 싶지 않은 경우 간간히 사용해야 하는 경우가 있다

```zsh
# 설정
git update-index --assume-unchanged 파일경로
# 해제
git update-index --no-assume-unchaged 파일경로
# assume-unchaged된 목록 보기
git ls-files -v | grep "^[[:lower:]]"
# 이것도 되는것 아닌가?
git ls-files -v | grep "^h\s"
```