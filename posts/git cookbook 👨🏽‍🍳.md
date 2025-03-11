---
title: git cookbook 👨🏽‍🍳
---
## 가끔 특정 파일 변경사항 신경 쓰고 싶지 않을 때

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

## remote로 push 하는데 502가 발생한다면?

다음과 같은 에러로 push가 실패하는 경우
```zsh
Enumerating objects: 640, done.
Counting objects: 100% (640/640), done.
Delta compression using up to 11 threads
Compressing objects: 100% (268/268), done.
error: RPC failed; HTTP 502 curl 22 The requested URL returned error: 502
send-pack: unexpected disconnect while reading sideband packet
Writing objects: 100% (593/593), 1.50 MiB | 8.60 MiB/s, done.
Total 593 (delta 337), reused 499 (delta 273), pack-reused 0
fatal: the remote end hung up unexpectedly
Everything up-to-date
```

업로드 하려는 사이즈가 큰 경우 buffer 사이즈 이슈로 config를 다음과 같이 수정해주면 된다

```zsh
git config --local http.postBuffer 157286400
```

## git이 파일명 디렉토리명 대소문자 제대로 인식 못 할 때
```git
# .git/config
[core]
	ignorecase = true # false로 바꿔준다
```

## 현재 브랜치가 remote와 얼마나 차이 나는지 확인

```zsh
git -vv | grep '^*'
* feature/branch                           9c5ec458 [origin/feature/branch: ahead 3, behind 2] commit message
```

이렇게 remote와 차이나는 부분을 보여준다. 물론 git fetch를 해야 최신 remote와 차이를 볼 수 있다
