### GUID

- 전 세계에서 유일하게 식별할 수 있는 고유한 128비트 값
- 중복 확률이 사실상 0에 가깝기 때문에 번호 관리하는 서버 없이도 어디서나 임의로 생성해서 식별자로 사용할 수 있음
- 보통 32개의 16진수 문자가 히이픈(-)으로 구분된 형태
    - 3f2504e0-4f89-11d3-9a0c-0305e82c3301
- 유니티에서 GUID는 에셋의 고유 주민등록번호 역할을 함
    - 파일 이름이나 폴더 경로가 바뀌어도 GUID는 절대 안변함

## AssetDatabase

### public static string[] FindAssets(string filter);

- 에디터 환경에서 프로젝트 내의 에셋을 GUID 배열로 반환하는 메서드

| **키워드** | **의미** | **설명 및 예시** |
| --- | --- | --- |
| **`t:`** | **Type** (타입) | 특정 클래스, 컴포넌트, 에셋 유형 검색
예: `t:Material`, `t:MonoScript`, `t:Scene` |
| **`l:`** | **Label** (라벨) | 인스펙터 창 하단에서 에셋에 지정한 라벨 검색
예: `l:Boss`, `l:UI` |
| **`v:`**  | **Version** (버전) | 에셋 스토어 패키지 등의 버전 상태 검색
예: `v:outdated` |
| **`a:`** | **Area** (영역) | 에셋의 검색 영역 지정 (기본값, 패키지 등)
예: `a:all` (Packages 폴더 포함 전체 검색) |
| **`b:`** | **Bundle** (에셋 번들) | 특정 에셋 번들에 포함된 에셋 검색
예: `b:MyAssetBundle` |

### public static string GUIDToAssetPath(string guid);

- guid: 에셋의 GUID 문자열
- 반환값(string): 에셋의 프로젝트 상대 경로

### public static T LoadAssetAtPath<T>(string assetPath) where T : UnityEngine.Object;

- 프로젝트 폴더 경로(Path)를 지정하여 특정 에셋을 메모리로 로드해오는 메서드
- `assetPath`: 반드시 “Assets\…”로 시작하는 프로젝트 상대경로
- `T`: 불러오고자 하는 에셋의 타입

## Path

### public static string GetFullPath(string path);

- 상대 경로나 지저분한 경로 기호가 섞인 문자열을 운영체제가 이해할 수 있는 절대 경로로 계산해주는 메서드

```csharp
string[] guids = AssetDatabase.FindAssets("t:MissionData");
if (guids == null || guids.Length == 0)
{
    _resultLog += "MissionData 에셋을 찾지 못했습니다.\n";
    return;
}

for (int i = 0; i < guids.Length; i++)
{
    string path = AssetDatabase.GUIDToAssetPath(guids[i]);
    MissionData mission = AssetDatabase.LoadAssetAtPath<MissionData>(path);
    if (mission == null)
    {
        _resultLog += $"[skip] 로드 실패: {path}\n\n";
        Repaint();
        continue;
    }
}
```

## JsonConvert

### public static string SerializeObject(object value)

- 전달받은 객체(value)를 기본 설정으로 Json문자열로 변환

## JObject

- JSON 텍스트 데이터를 C#에서 `root["content"]` 처럼 키(Key) 이름으로 손쉽게 다룰 수 있게 해주는 JSON 전용 객체 타입
- 서버(Claude)에서 받아온 값은 단순한 긴 텍스트 문자열(string)임

```csharp
// 서버에서 받아온 문자열 예시
{
  "id": "msg_01123",
  "type": "message",
  "role": "assistant",
  "content": [
    {
      "type": "text",
      "text": "미션 검토 결과입니다..."
    }
  ]
}

```

- 이 긴 글자 뭉치에서 "미션 검토 결과입니다..."라는 진짜 답변만 쏙 뽑아내려면 문자열을 하나하나 자르는 것이 아니라, `JObject.Parse(responseBody)`를 통해 JSON 구조로 변환해 주어야 함
- `JObject`로 변환하고 나면, 대괄호 `[]` 안에 키 이름을 넣어서 아주 간단하게 데이터에 접근할 수 있음

```csharp
// 1. responseBody 문자열을 JObject 구조로 파싱
JObject root = JObject.Parse(responseBody);

// 2. 키(Key) 이름으로 원하는 값 접근
string id = root["id"].ToString(); // "msg_01123"
string role = root["role"].ToString(); // "assistant"

// 3. 배열이나 중첩된 JSON 구조 안으로 들어가기
string text = root["content"][0]["text"].ToString(); // "미션 검토 결과입니다..."
```

- Json 처리 방식: `JObject`vs `JsonConvert.DeserializeObject<T>`

| **구분** | **JObject (JObject.Parse)** | **JsonConvert.DeserializeObject<T>** |
| --- | --- | --- |
| **방식** | 별도의 C# 클래스 없이 **키 이름으로 직접 접근** | C# **클래스(Class)를 미리 정의**해 두고 1:1 변환 |
| **장점** | C# 클래스 구조를 일일이 안 만들어도 됨 | 타입이 명확하고 C# 코드 가독성이 좋음 |
| **단점** | 키 이름을 오타 내면 실행 시 에러 발생 | JSON 구조에 맞는 DTO 클래스를 직접 선언해야 함 |
- Claude API 응답처럼 JSON 구조가 복잡하거나 깊은데, 그중 특정 텍스트 필드 하나만 쏙 꺼내고 싶을 때 C# 클래스를 일일이 만드는 대신 `JObject`를 쓰는 것이 훨씬 간편

## JToken

- JSON 안에 들어있는 데이터라면 타입 상관없이 무조건 담아둘 수 있는 C#의 JSON 만능상자

```csharp
JToken textToken = root["content"]?[0]?["text"];
```

- `root["content"]?[0]?["text"]`
    - 응답 JSON 구조는 계단처럼 겹겹이 싸여있음(하단참고)
        
        ```csharp
        {                          <-- root (JObject)
          "content": [             <-- root["content"] (배열)
            {                      <-- root["content"][0] (첫 번째 아이템)
              "text": "안녕하세요"   <-- root["content"][0]["text"] (진짜 답변!)
            }
          ]
        }
        ```
        
    - 이 계단을 타고 root → content → 첫 번째(0번) → text 경로로 들어가서 “안녕하세요” 라는 글자 집어오는 과정
    - `?`: 비어있거나 없을 때 에러 방지용