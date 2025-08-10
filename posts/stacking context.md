각 엘리먼트가 3차원 축 z axis(`z-index`)를 기반으로 자식 요소의 `z-index`는 부모를 기준으로 별도의 context를 가지게 된다.  예를 들어 A 부모 요소의 `z-index`가 2일 때 다른 부모의 형제 요소 B의 `z-index`가 3이면 A의 자식 요소의 `z-index`가 9999여도 B 요소 오래 위치한다.
- [MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_positioned_layout/Stacking_context)
- https://developer.mozilla.org/en-US/play
