### API 호출하여 AI 사용 과정

1. **API 키 로드**
    - API 키는 코드에 하드코딩하지 않고 환경변수나 보안 저장소에서 불러옴
    - 단, 이 프로젝트의 `ClaudeBalanceReviewer`는 에디터 전용 툴(Editor/ 폴더)이라 `Environment.GetEnvironmentVariable`로 OS 환경변수를 읽는 방식이 가능함
    - 모바일 등 런타임 빌드에서 인게임 AI 기능을 만든다면 OS 환경변수 접근이 안 되므로, API 키를 클라이언트에 두지 않고 백엔드 프록시 서버를 거치는 구조가 일반적
        
        ```csharp
        string apiKey = Environment.GetEnvironmentVariable("ANTHROPIC_API_KEY");
        ```
        
2. **요청 데이터 모델 정의 + 응답 파싱 방식(선택)**
    - 요청 Body는 보낼 JSON 구조에 맞춰 C# 클래스(또는 익명 객체)로 작성
    - 응답은 모델(클래스)을 따로 만들지 않고, JObject/JToken으로 필요한 필드만 그때그때 꺼내는 "부분 역직렬화" 방식 선택
        
        ```csharp
        object requestBody = new
        {
            model = "claude-sonnet-4-6",
            // 한 번의 응답에서 생성할 수 있는 최대 토큰
            max_tokens = 700,
            messages = new[] { new { role = "user", content = prompt } }
        };
        
        string json = JsonConvert.SerializeObject(requestBody);
        StringContent content = new StringContent(json, Encoding.UTF8, "application/json");
        ```
        
3. **HTTP 클라이언트 구성**
    - 비동기 네트워크 통신 담당 메서드를 구현
    - 에디터 툴에서는 `HttpClient` 사용 가능 (이 프로젝트가 채택한 방식)
    - 런타임(모바일/WebGL 등) 빌드에서는 IL2CPP, 플랫폼 호환성 때문에 `UnityWebRequest`가 더 권장됨
        
        ```csharp
        private static readonly HttpClient Client = CreateClient();
        
        private static HttpClient CreateClient()
        {
            HttpClient client = new HttpClient();
            client.Timeout = TimeSpan.FromSeconds(60);
            return client;
        }
        ```
        
4. **인증 헤더 및 Request Body 설정**
    - 인증 방식은 API 제공사마다 다름
    - Body에는 모델명, 프롬프트, 옵션 파라미터를 JSON 문자열로 담아 전송
        
        ```csharp
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Post, "https://api.anthropic.com/v1/messages");
        // Anthropic(Claude) API는 다음 두 헤더 조합을 사용:
        request.Headers.Add("x-api-key", apiKey);
        request.Headers.Add("anthropic-version", "2023-06-01");
        ```
        
5. **비동기 API 요청 발송**
    - `async`/`await`(또는 코루틴)로 메인 스레드 블로킹 없이 서버에 HTTP POST 요청
        
        ```csharp
        HttpResponseMessage response = await Client.SendAsync(request);
        ```
        
6. **응답 수신 및 파싱**
    - HTTP 상태 코드로 에러 여부 확인
        
        ```csharp
        string responseBody = await response.Content.ReadAsStringAsync();
        
        if (!response.IsSuccessStatusCode)
        {
            Debug.LogError($"Claude API 실패 ({(int)response.StatusCode}): {responseBody}");
            return null;
        }
        ```
        
    - 정상 수신 시 JSON을 C# 객체로 역직렬화(전체든 부분이든)
        
        ```csharp
        JObject root = JObject.Parse(responseBody);
        JToken textToken = root["content"]?[0]?["text"];
        ```
        
    - 응답은 스키마 전체를 클래스로 만들 필요 없이 필요한 필드만 뽑아 쓰는 부분 역직렬화도 실무적으로 많이 씀
        - AI 서버에 요청보냈을 때 돌아오는 JSON 스키마는 구조 복잡하고 큼
        → 필요한 부분만 역직렬화해서 가져옴
        - 정석적인 방법: 스키마 전체를 클래스로 만드는 방식


---

## HttpClient

- C#에서 웹 서버와 HTTP 통신(데이터 주고받기)을 하기 위해 제공하는 클래스
- 내부적으로 OS에게 소켓을 빌려와서 알아서 관리해줌
- 소켓 고갈을 방지하고 메모리를 효율적으로 사용하기 위해 static readonly로 선언
- `static`으로 단 하나만 유지할 경우:
    1. 하나의 `HttpClient`가 내부적으로 커넥션 풀(Connection Pool)을 형성하여 소켓 재사용(Socket Reuse)을 수행
    2. 소켓을 닫고 다시 여는 수고가 없어지므로 네트워크 연결 핸드셰이크(TCP/TLS) 비용이 절약되어 통신 속도도 훨씬 빨라짐
- 요청할 때마다 `new`를 쓸 경우
    1. 요청이 끝난 후 `Dispose()`를 호출해도, OS 단에서는 안전한 통신 종료를 위해 해당 소켓을 즉시 닫지 않고 **`TIME_WAIT`** 상태로 일정 시간(보통 1~4분) 방치
    2. 만약 짧은 시간 동안 미션 검수 요청을 여러 번 보내면, `TIME_WAIT` 상태의 소켓이 기하급수적으로 쌓임
    3. 결국 사용할 수 있는 소켓이 바닥나서 `SocketException` (원격 서버에 연결할 수 없음) 에러가 발생하며 프로그램의 네트워크 통신이 완전히 먹통됨
- `readonly`를 사용하는 이유: 안전성 보장

### Timeout

- 네트워크 관련 객체에서 응답을 받기 위해 최대 몇 초까지 기다릴 것인지 지정

### TimeSpan

- 20초, 5분 과 같은 시간의 길이를 나타내는 타입
- `TimeSpan.FromSeconds(60)` : 60초
- `TimeSpan.FromMinutes(2)` : 2분

```csharp
client.Timeout = TimeSpan.FromSeconds(60);
```

### public Task<HttpResponseMessage> SendAsync(HttpRequestMessage request)

- 요청 메세지를 서버로 발송하고 응답이 올 때까지 비동기로 기다림
- 데이터 전송 네트워크 지연시간(RTT)과 서버의 처리 시간을 기다려야함
⇒ 네트워크망을 통해 서버와 통신하는 과정이 비동기로 일어남
- `request`: 요청 객체
- 반환값(`Task<HttpResponseMessage>`): 비동기 작업이 완료되면 서버의 응답(`HttpResponseMessage`) 반환

### public Task<string> ReadAsStringAsync()

- 서버에서 넘어온 응답 데이터(스트림/바이트)를 비동기로 읽어서  C# string 텍스트로 변환
- 본문 데이터가 크면 패킷이 여러 개로 나누어져 들어오므로 모든 패킷이 도착할 때까지 네트워크 I/O 대기가 발생
& 수신된 스트림 버퍼를 읽고 문자열로 인코딩하는 I/O 작업이 수반됨
⇒ 서버가 보내준 데이터를 읽어서 텍스트로 바꾸는 과정 비동기로
- 반환값(`Task<string>`): 읽어들인 응답 본문(Body) 데이터를 C# `string` 문자열로 반환

```csharp
HttpResponseMessage response = await Client.SendAsync(request);
string responseBody = await response.Content.ReadAsStringAsync();
```

### SendAsync, ReadAsStringAsync가 둘 다 비동기로 처리되는 이유

- 데이터가 이동하는 물리적 장치와 스레드가 일하는 방식이 완전히 다르기 때문

---

## Environment

- .Net framework가 제공하는 ‘현재 실행 중인 프로그램의 환경 정보 및 운영체제 정보’를 담고 있는 클래스
- C# 프로그램이 자신이 실행되고 있는 컴퓨터나 OS 상태에 대해 질문하고 값을 가져올 수 있게 해주는 창구 역할
- static클래스 → 모든 멤버가 static으로 이루어져 있어 별도의 객체 생성 없이 바로 사용 가능

### public static string? GetEnvironmentVariable(string variable)

- 현재 프로세스 및 시스템 환경변수에서 지정한 이름의 값 검색
- variable: 읽어올 환경변수 이름 → ‘시스템 환경 변수 편집’에서 미리 설정해야함
- 반환값(string?): 해당 이름의 환경변수 값(문자열) 반환, 변수가 존재하지 않으면 null 반환

---

## StringContent

- HttpClient를 통해 HTTP 요청을 보낼 때 본문에 담을 문자열 데이터(JSON 등)를 HTTP 규격에 맞게 포장해 주는 C# 클래스
- 웹 통신(HTTP)는 단순 텍스트만 던지는 게 아니라 이 데이터의 형태가 무엇인지(JSON인지 HTML인지) 어떤 문자로 인코딩 되었는지에 대한 부가 정보를 함께 요구
→ StringContent는 단순 문자열에 이러한 HTTP 통신용 메타데이터를 씌워 HttpContent 객체 형태로 변환해 주는 상자 역할을 함

```csharp
// json: 실제 전송할 본문 문자열
// Encoding.UTF8: 한글이나 특수문자가 깨지지 않도록 인코딩 방식 지정
// "application/json": HTTP Header(부가정보)의 Content-Type 지정
StringContent content = new StringContent(json, Encoding.UTF8, "application/json");
```

---

## HttpRequestMessage

- C#에서 서버로 보낼 HTTP 요청 메세지 전체를 직접 조립하고 구성할 수 있게 해주는 클래스
- HTTP 메세지의 구성 요소를 C# 코드로 하나로 묶어 만들어내는 규격화된 요청 객체

```csharp
// 편지 뼈대 생성
HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Post, "https://api.anthropic.com/v1/messages");
// 헤더 영역에 정보 추가
// API 인증 키 추가
request.Headers.Add("x-api-key", apiKey);
// Anthropic API 규격 버전 추가
request.Headers.Add("anthropic-version", "2023-06-01");
// 바디 영역에 데이터 집어넣기
request.Content = content;
```

- `request`: HTTP 문법을 따른 편지 뼈대
- `"https://api.anthropic.com/v1/messages"`
    - 요청 편지를 받을 서버의 정확한 상세 인터넷 주소
    - Anthropic이 자기네 서버를 외부 개발자들이 사용할 수 있도록 공식 문서에 명시해 둠
    → Claude가 아니라 GPT를 쓴다면 달라짐
    
    | **주소 요소** | **실제 값** | **비유 및 역할** |
    | --- | --- | --- |
    | **프로토콜** | `https://` | "이 편지는 암호화된 안전한 방식(HTTPS)으로
    전달해 주세요." |
    | **도메인 (서버 주소)** | `api.anthropic.com` | "Anthropic 회사의 API 건물"로 찾아가세요. |
    | **버전 (Version)** | `/v1` | "그 건물 안의 1번 버전(v1) 창구로 가세요." |
    | **엔드포인트 (Endpoint)** | `/messages` | "v1 창구 중에서도 '메시지/답변 생성'을 담당하는 세부 부서로 가세요." |
- `apiKey`: 내 계정 전용으로 발급받은 비밀 인증키 문자열
- `"x-api-key"`,  `"anthropic-version”`, `"2023-06-01”`: Anthropic 가이드 문서에서 지정해둔 값들

---

## HttpResponseMessage

- 서버가 처리를 마친 뒤 클라이언트(나)에게 돌려준 '응답 편지 봉투' 전체를 표현하는 C# 클래스

### IsSuccessStatusCode

- 서버가 보낸 응답이 성공(200번대)인지 아닌지를 bool로 알려주는 프로퍼티

---

## HttpMethod

- 서버가 이 요청 받아서 어떤 일을 해야하는지 알려주는 행동 명령어

| **HttpMethod** | **의미** | **역할** | **현실 비유** |
| --- | --- | --- | --- |
| **`HttpMethod.Get`** | **조회** | 서버에 있는 데이터를 읽어오기만 함 (서버 데이터 변경 X) | "서버야, 내 캐릭터 정보 좀 보여줘" |
| **`HttpMethod.Post`** | **생성/전송** | 새로운 데이터를 서버로 보내서 처리/저장함 | "서버야, 내가 작성한 글(또는 질문) 좀 등록해 줘" |
| **`HttpMethod.Put`** | **수정** | 서버에 이미 있는 데이터를 새로운 내용으로 덮어씀 | "서버야, 내 닉네임을 다른 걸로 바꿔줘" |
| **`HttpMethod.Delete`** | **삭제** | 서버의 특정 데이터를 지움 | "서버야, 내 저장된 세이브 파일 좀 삭제해 줘" |