
> Upfront definition of the API contracts

FE와 BE가 병렬로 개발이 가능하도록 하는 일종의 명세를 경계에 두고 서로 개발을 할 수 있는 방법 같은데

> A single source of truth for the API contracts

FE와 BE가 병렬로 개발하려면 git과 같이 어떤 중간에 소스를 저장하는 저장소 같은게 있고 BE와 FE는 각자 클리언트에서 pull & push를 통해 소스를 같이 동기화하는 메커니즘이 필요해 보인다
예를 들어 graphQL과 같이 graphQL 서버에서 스키마를 제공하고 그 스키마가 약속(contract)
