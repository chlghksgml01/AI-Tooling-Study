## MCP(Model Context Protocol)
- Anthropic이 개발하여 오픈소스로 공개한 AI 모델과 외부 데이터/도구 간의 표준 연결 프로토콜
- 기존에는 AI(LLM)를 특정 데이터베이스, 파일 시스템, API, 외부 툴 등과 연결하려면 매번 개별적으로 API 커스텀 코드를 작성했어야 함
- MCP는 이를 하나의 공통 규격으로 통일하여 USB 단자처럼 AI 애플리케이션과 외부 시스템을 손쉽게 연결해 줌

### 주요 특징
- 클라이언트 - 서버 구조
    - MCP Client(소비자): Claude Desktop, IDE(Cursor 등)와 같이 AI 서비스를 제공하는 주체
    - MCP Server(제공자): 로컬 파일, GitHub, Postgres DB, Slack 등 데이터나 기능을 보유하고 제공하는 주체
- 통신 포맷: JSON-RPC 2.0
    - 전송 방식(STDIO/Streamable HTTP)과 무관하게 모든 메시지는 JSON-RPC 2.0 규격(메서드명 + 파라미터 + id)으로 주고받음
- initialize / Capability Negotiation(기능 협상)
    - 연결 시작 시 클라이언트와 서버가 `initialize` 요청/응답을 주고받으며 서로 어떤 기능을 지원하는지 먼저 협상하는 핸드셰이크 과정
    - 모든 MCP 클라이언트와 서버가 똑같은 기능을 지원하지 않기 때문에 필요한 과정
- 핵심 Primitives
    - 서버가 클라이언트(LLM)에 노출하는 3가지
        - Resources(자원 - Read Only): 파일 읽기, 데이터베이스 조회 등 외부 데이터 접근
        - Tools(도구 - Executable): 특정 함수 실행, API 호출 등 작업 수행
        - Prompts(프롬프트 - Templates): 자주 사용되는 프롬프트 템플릿 관리
    - 반대로 클라이언트가 서버에 노출하는 2가지
        - Sampling: 서버가 클라이언트에게 "LLM 호출 좀 대신 해줘"라고 요청하는 기능
        - Roots: 클라이언트가 서버에게 파일 시스템 접근 범위(루트 경로)를 알려주는 기능
    - Elicitation(추가 정보 입력 요청): 서버가 전달받은 명령을 처리하는 도중 스스로 결정할 수 없는 정보가 필요할 때 작업을 잠시 멈추고 사용자에게 직접 질문(프롬프트)을 띄워 입력을 받아내는 기능
- 보안성: 로컬 환경에서 실행할 수 있어 데이터가 외부로 나가지 않고 서버 단위로 API Key, 접근 가능 폴더 범위 등 권한을 사용자가 개별적으로 제어할 수 있어 민감한 데이터 유출 위험 줄어듦

### 장점
- 개발 생산성 향상: 개별 AI 플랫폼마다 연동 코드를 새로 작성할 필요 없이 하나의 MCP Server만 만들어두면 다양한 MCP 지원 클라이언트에서 즉시 사용 가능
- 생태계 확장: 다양한 커뮤니티 및 개발자들이 GitHub, Notion 등 연동되는 MCP Server를 공유하고 있어 생태계 빠르게 확장
- MCP Registry: 공식 서버 디렉토리(modelcontextprotocol.io/registry)가 생겨 원하는 MCP 서버를 검색, 설치하기 쉬워짐

---

## MCP 서버
- MCP 규격을 준수하여 LLM(클라이언트)에 외부 데이터, 실행 권한, 워크플로 템플릿을 제공하는 경량 애플리케이션/프로세스

❓ MCP 서버 종류 다양한 이유

❗특정 서비스나 자원별로 전용 어댑터 역할을 함
→ 웹 브라우저 하나로 네이버, 구글 등 모두 접속할 수 있지만 각 사이트의 기능이 다른 것처럼 MCP 규격은 하나지만 AI가 연결되어 일해야 하는 외부 시스템이 전부 다르기 때문에 종류가 많음

❗하나의 거대한 통합 MCP 서버가 모든 권한을 다 갖고 있으면 보안상 매우 위험함

❗기능별로 MCP 서버를 나누어 두면 사용자는 필요한 MCP 서버만 골라서 켜고 각 서버별 권한(API Key, 접근 가능 폴더 범위 등)을 개별적으로 제어할 수 있음

❗종류
- GitHub MCP 서버: GitHub REST API / GraphQL API를 호출하여 PR(Pull Request)을 생성하거나 이슈를 읽는 로직 구현
- Postgres MCP 서버: DB 커넥션을 맺고 SQL 쿼리를 실행하여 결과를 파싱하는 로직 구현
- FileSystem MCP 서버: OS의 파일 시스템 접근 권한을 관리하며 파일 읽기/쓰기 수행

### 동작 방식
- STDIO(Standard Input/Output)
    - 로컬 컴퓨터 내부에서 자식 프로세스 형태로 실행
    - 표준 입출력을 통해 데이터를 주고받으므로 외부 네트워크 노출 없이 높은 보안성과 빠른 속도 제공
- Streamable HTTP
    - 원격 서버에 구축되어 HTTP 네트워크를 통해 클라이언트와 통신
    - 단일 HTTP 엔드포인트로 요청(POST)을 받고 필요 시 SSE(Server-Sent Events)로 응답을 스트리밍하는 방식 (기존 HTTP+SSE 전송 방식을 대체)
    - 인증: 원격 서버 접근 시 OAuth 2.1 기반 인증(Authorization) 표준을 지원하여 아무나 접속하지 못하도록 제어
    - 분산 환경이나 사내 공용 MCP 서버를 구축할 때 활용

### Tools 동작 구조(호출 흐름 Discovery → Invocation)
- Discovery: 클라이언트가 서버에 `tools/list`(또는 `resources/list`, `prompts/list`)를 요청해 현재 사용 가능한 도구/자원/프롬프트 목록과 파라미터 스키마를 조회
    - 클라이언트가 목록을 미리 하드코딩해두는 게 아니라 연결될 때마다 서버에게 직접 물어봐서 동적으로 알아냄
- Invocation: LLM이 사용자 요청을 보고 어떤 도구가 필요한지 판단하면 클라이언트가 `tools/call`로 도구 이름 + 파라미터를 담아 서버에 실행 요청
- Result: 서버가 실제 로직(API 호출, DB 조회 등)을 수행한 뒤 결과를 content(text/image 등) 형태로 응답 → 클라이언트가 이를 LLM에게 다시 전달해 최종 답변 생성에 활용
- ps. Discovery, Invocation: 분산 시스템, RPC 등에서 사용되는 일반적인 패턴 용어