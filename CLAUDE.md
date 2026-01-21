## Sub-agent Delegation Rules

- **테스트 자동화**: Java/Spring Boot 관련 비즈니스 로직(Service, Controller, Repository 등)의 구현이나 수정이 완료되면, **별도의 사용자 지시가 없어도** 즉시 `test-runner` sub-agent를 호출하여 다음을 수행한다.
    - 호출 시 수정된 파일 경로를 인자로 전달한다.
    - `test-runner`가 해당 파일에 대한 JUnit 5 테스트를 작성하거나 업데이트하도록 지시한다.
    - 테스트 실행 결과(성공/실패)를 메인 세션에 요약 보고하게 한다.
- **연속 작업**: 테스트가 실패할 경우, `test-runner`가 스스로 원인을 파악해 수정하거나 메인 에이전트에게 수정을 요청하도록 흐름을 유지한다.