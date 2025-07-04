# intoday87.github.io 정리의 공간

## 반복되는 시간을 줄이고 싶은 이유

1. 연차가 거듭될수록 모르는 것도 물론 아직 너무 많은데 아는 것도 많아지는 만큼 까먹는 것도 매우 많아진다. 알았던 것을 오랜만에 다시 들춰보게 되었을 때 기억나지 않으면 다시 찾아봐야 하고 어떤 것은 마치 처음처럼 다시금 문제 해결 과정에 시간을 소비한다. 그 과정이 반복되다 보니 내가 언제든 다시 찾아볼 수 있는 정리 공간이 필요하다

2. 시간이 지나면서 깨달은게 한 가지 더 있다. 우리 주변의 또는 인터넷 상의 익명의 블로그를 작성해주신 고수분들의 정리를 보고 도움을 때로는 빠르고 정확하게 얻어 가지만 영원한 것은 없다.  블로그의 주소가 변경되거나 블로그 계약 기간이 끝나 없어지거나 또는 현 상황에는 맞지 않은 옛것이 되기도 한다. 그리고 중요한 것은 그 글에서 모든 부분을 필요로 얻어가지 않을 확률도 클 뿐더러 1번과 마찬가지고 내가 어느 부분에서 핵심을 얻어 갔는지에 대해 갈피를 잡지 못하는 경우가 종종 있다. 즉 내가 얻어가는 부분에 대해 정리가 필요하다

3. 내가 이해한 부분이 어디까지 이해했는지 시간이 지나면서 갈피를 못 잡는 경우가 종종 있다. 참고했었던 글을 다시 보면 내가 이해했던 부분이 어디까지 생각이 안 나서 처음처럼 다시 읽고 깨우치거나 다시 읽어보니 이해했던 부분이 잘못되었거나 하는 경우가 있다. 내가 어디까지 이해했는지 링크 대신 본문에 직접 적어놓고 추후에 다시 필요했던 부분을 읽어서 빠르게 캐치를 하던지 현 상황에 맞게 업데이트를 하거나 잘못 이해해서 적어놓은 부분을 고쳐놓을 필요성이 있다

## git 환경으로 갈아타는 이유

notion이 물론 미디어 삽입시 미리보기 기능이나 풍부한 기능들이 좋지만 일반적이지 않은 네트워크 상황(보안이 된 사내망) 에서는 특히 느리다. 빠르게 보고 확인해야 하는 상황에서 로딩 타임도 한 몫 한다. 그래서

-  github pages 사용
	- git으로 버전관리 용이
	- github markdown language를 사용하여 페이지를 구성할 수 있는 장점 -> editor 환경에서도 동일하게 markdown language로 작업할 수 있다. 
	- md파일  수정에 최적화 된 [obsidian](https://obsidian.md/) + [git plugin](https://github.com/Vinzent03/obsidian-git) 으로 주기적인 commit & push
		- 완전 동일하지는 않지만(특히 이미지 업로드) github 웹으로 접근시 editor와 거의 같은 포맷으로 보여줄 수 있다

## 페이지 구성

- 주제별 페이지를 디렉토리 하위로 묶지 않고 태그 기반으로 검색이 가능하도록 페이지 구성해볼 예정
	- 디렉토리 별로 구성해서 해보니까 두 주제에 겹치는 경우가 많아 한 주제에 하위로 두기 어렵다

## 📚 Posts

<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>
