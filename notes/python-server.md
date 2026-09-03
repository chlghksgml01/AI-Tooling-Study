### httpx2.AsyncClient

- 비동기 HTTP 요청을 보내기 위한 클라이언트 객체 (동기 버전은 `httpx.Client`)
- 하나의 클라이언트로 여러 요청을 보내면서 커넥션(TCP 연결)을 재사용하고 공통 설정(`headers`, `timeout`, `base_url` 등) 공유
- `async with` 로 열고 닫음 → 블록을 벗어나면 에러가 나든 안 나든 자동으로 연결 정리(`close`)

```python
async with httpx2.AsyncClient() as client:
    response = await client.get(url, headers=headers, timeout=30.0)
```

- 블록 안에서 `await client.get(...)`, `await client.post(...)` 등으로 요청
- `client.get()` 은 코루틴을 반환하므로 반드시 `await` 를 붙여야 실제 응답이 나옴

### get

- HTTP 요청 라이브러리의 GET 요청 메서드
- `requests` 는 동기 전용 / `httpx`(`httpx2` 포함) 는 동기·비동기 둘 다 지원
- 이 프로젝트는 비동기 버전 사용 → `async with httpx2.AsyncClient() as client:` 안에서 `await client.get(...)`
- 아래 시그니처는 동기 `requests.get` 기준 (httpx도 파라미터 이름은 거의 동일)

```python
requests.get(url, params=None, headers=None, timeout=None, **kwargs)
```

- `url` (`str`): 접속할 웹 서버 또는 API 주소
- `params` (`dict`, 선택): URL 뒤에 붙는 쿼리 스트링 파라미터 (`?key=value`)
- `headers` (`dict`, 선택): 요청 시 보낼 헤더 정보 (`User-Agent` 등)
- `timeout` (`float`, 선택): 응답을 기다릴 최대 제한 시간(초)
- `**kwargs`: 자주 쓰이는 주요 매개변수 외에도 내부 통신 엔진이 지원하는 다양한 부가 옵션들을 얼마든지 넘겨도 좋다는 의미

### httpx.Response

- 서버가 응답한 모든 정보(상태 코드, 데이터, 헤더 등)를 담고 있음

| **속성 / 메서드** | **타입 / 반환 타입** | **설명** |
| --- | --- | --- |
| **`response.status_code`** | `int` | HTTP 상태 코드 (예: `200`, `404`, `500`) |
| **`response.is_success`** | `bool` | 상태 코드가 200번대(성공)인지 여부 (`True`/`False`) |
| **`response.json()`** | `dict` 또는 `list` | 서버가 준 JSON 데이터를 파이썬 객체로 변환 |
| **`response.text`** | `str` | 응답 본문 전체를 텍스트(문자열)로 가져옴 |
| **`response.content`** | `bytes` | 응답 본문을 원시 바이너리(바이트) 데이터로 가져옴 (이미지/파일 다운로드 시 사용) |
| **`response.raise_for_status()`** | 예외 발생 또는 self | 에러 응답(4xx·5xx)이면 예외 발생(`httpx.HTTPStatusError`). 성공 시 `requests` 는 `None`, `httpx` 계열은 response 객체 반환 |

### @mcp.tool

- AI(LLM)가 실행할 수 있는 도구를 등록하는 데코레이터
- 함수의 설명서(도움말) 전달
    - 함수의 이름, docstring(""" """), 매개변수 타입힌트를 읽어서 AI에게 건네줌 (`#` 주석은 안 읽힘)
    - AI는 이 정보를 보고 어떤 도구를 언제 어떤 값으로 호출할지 판단
        - 예: 사용자가 "캘리포니아 날씨 경보 알려줘" → AI가 `get_alerts(state="CA")` 호출
- AI가 대화 중 도구를 쓰기로 결정하면 `@mcp.tool` 로 등록된 파이썬 함수가 실제로 백엔드에서 실행되고 그 결과가 다시 AI에게 전달
- 함수 선언 바로 밑에 독스트링(""" """)을 쓰면 그 내용이 AI가 읽는 '도구 설명'이 됨 (문법적 강제는 아니고 동작 방식)
    
    ```python
    @mcp.tool()
    async def get_alerts(state: str) -> str:
        """Get weather alerts for a US state.  <-- 1. AI가 읽는 '도구 설명'
    ㅇㄹㅇㄹㄹ
        Args:
            state: Two-letter US state code   <-- 2. AI가 읽는 '파라미터 입력 규칙'
        """
    ```

### MCPServer

- `mcp = MCPServer("weather")` : MCP 서버 인스턴스 생성 ("weather" = 클라이언트에 보이는 서버 이름)
- 클라이언트 연결 시 initialize 핸드셰이크, 툴 목록 관리 담당

### 서버 실행

```python
if __name__ == "__main__":
    mcp.run(transport="stdio")
```

- `transport="stdio"` : 로컬 표준입출력으로 통신. 클라이언트가 이 스크립트를 자식 프로세스로 실행함
- stdio 방식에서는 `print()` 금지 → stdout 은 JSON-RPC 전용이라 일반 텍스트가 섞이면 통신이 깨짐. 로그는 `logging`(stderr) 사용