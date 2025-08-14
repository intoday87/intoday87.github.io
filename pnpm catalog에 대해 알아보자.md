https://pnpm.io/catalogs

workspace feature로 workspace 내에서 일관된 버전 사용 목적으로 나온 것으로 보이고 다음과 같은 장점이  있다

- Maintain unique versions - catalog로 버전 사용시 workspace내에서 유일한 버전을 가지게 된다. 각 workspace에서 같은 `^`, `~` 라도 설치시 다른 버전으로 resolve 될 수 있는 문제를 해결하고 있는 것으로 보인다. `pnpm dedupe`로 전체 의존성 트리를 정돈하는 케이스가 발생하지 않을수도 있을것 같다
- Easier upgrades - 