# claude-lab

Claude 코드를 활용하면서 만든 유용한 스킬, 커맨드, 도구들을 모아둔 개인 저장소

### Skills

* readme-generator : Java/Spring 마이크로서비스용 README.md 파일 생성 스킬
* api-integrator : 마이크로서비스 Swagger 문서를 참고하여 TypeScript 기반 API 호출 코드를 자동 생성하고, API Gateway를 통해 엔드포인트와 통합하는 스킬. 프론트에서 백엔드 MSA 서비스와 연결할 때 사용
* spring-global-exception-handler : 전역 오류 처리기 생성 스킬

### Sub Agents

* test-runner
  * 메인 에이전트가 수정한 Java 클래스의 유닛 테스트 작성 및 검증 수행
  * CLAUDE.md 지침을 통해 모든 코드 수정 시 자동으로 호출 및 실행되도록 구성

### Commands
