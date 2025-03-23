---
title: react 17에서 jsx runtime을 분리한 이유
---
https://legacy.reactjs.org/blog/2020/09/22/introducing-the-new-jsx-transform.html
기억이 안나서 다시 보면 결론은 complie시 `React.createElement`로 치환되는게 문제가 있고 jsx만 있는 환경에서 react가 import되지 않으면 문제가 되었던게 이 이슈였음. runtime을 별도로 분리하면 react를 불필요하게 매번 jsx에서 import할 필요가 없고 번들링시 react를 import하지 않음으로써 사이즈 감소도 있다고 함