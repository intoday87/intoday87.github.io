SSD를 대변하는 글인지는 모르겠으나 [medium](https://medium.com/@hintology/sdd-schema-driven-development-f1d232d73ea6)의 글을 읽어 봤다
Schema Driven Development의 약자로 보인다. 
요건중 공감이 많이 가는 몇 가지가 있어서 끄적여 본다

> **Upfront definition of the API contracts** which improves the velocity for both Backend and Mobile teams. This allows the development to be done in parallel.

FE와 BE가 병렬로 개발이 가능하도록 하는 일종의 명세를 경계에 두고 서로 개발을 할 수 있는 방법 같은데

> A **single source of truth** for the API contracts that is used for request validations and the API documentation to eliminate discrepancies.

FE와 BE가 병렬로 개발하려면 github과 같이 어떤 중간에 소스를 저장하는 저장소 같은게 있고 BE와 FE는 각자 클리언트에서 pull & push를 통해 소스를 같이 동기화하는 메커니즘이 필요해 보인다
예를 들어 graphQL과 같이 graphQL 서버에서 스키마를 제공하고 그 스키마가 약속(contract)가 되어 fe에서는 해당 스키마를 기반으로 코드 생성이 되어 타입 지원이 되는 개발과 같은 메커니즘으로 보인다.

하지만 [SSD 글](https://medium.com/@hintology/sdd-schema-driven-development-f1d232d73ea6)에서는 별도의 서버를 두지 않더라도 be에서 open api 스펙을 지원해서 fe에서 코드 생성을 할 수 있는 방법을 지원하는 방식 글에 소개 되었다. swager가 open api 3.1이상의 버전을 지원하면 open api fetch와 같은 open api를 지원하는 라이브러리를 통해 타입 지원을 쉽게 받을 수 있다. 주기적으로 json을 땡겨와야 하는 별도의 장치가 필요하긴 하지만 타입 지원을 통해 변경된 부분에 대해 쉽게 감지할 수 있다
