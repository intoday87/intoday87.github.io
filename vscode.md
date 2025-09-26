# <img src="https://code.visualstudio.com/assets/favicon.ico" width="20" /> vscode

## 갑자기 [Open symbol by name](https://code.visualstudio.com/docs/editing/editingevolved#_open-symbol-by-name) 으로 검색시 같은 symbol이 중복으로 뜨는 경우

파일도 같고 심지어 파일내 symbol도 하나 뿐인데 검색이 중복으로 똑같은것이 나타날때가 있다
그럴 때는 mac 기준 vscode를 종료하고

```zsh
rm -rf ~/Library/Application\ Support/Code/User/workspaceStorage
```

이 스토리지를 날려버리자. 날리면(not biden ❌) 에디터를 다시 열면 작업중인 창들 기록 및 히스토리 같은게 다 날아간다
