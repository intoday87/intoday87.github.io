
> Upfront definition of the API contracts

FE와 BE가 병렬로 개발이 가능하도록 하는 일종의 명세를 경계에 두고 서로 개발을 할 수 있는 방법 같은데

> A single source of truth for the API contracts

FE와 BE가 병렬로 개발하려면 git과 같이 어떤 중간에 소스를 저장하는 저장소 같은게 있고 BE와 FE는 각자 클리언트에서 pull & push를 통해 소스를 같이 동기화하는 메커니즘이 필요해 보인다
예를 들어 graphQL과 같이 graphQL 서버에서 스키마를 제공하고 그 스키마가 약속(contract)가 되어 fe에서는 해당 스키마를 기반으로 코드 생성이 되어 타입 지원이 되는 개발과 같은 메커니즘으로 보인다.

하지만 SSD글에서는 별도의 서버를 두지 않더라도 be에서 open api 스펙을 지원해서 fe에서 코드 생성을 할 수 있는 방법을 지원하는 방식 글에 소개 되었다
swagger를 
