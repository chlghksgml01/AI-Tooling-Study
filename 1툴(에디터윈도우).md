## EditorWindow

- 유니티 에디터 전용 윈도우 창 정의하는 클래스

## 속성

### [MenuItem("BlockPuzzle/AI 밸런스 검수")]

- 유니티 에디터 최상단 툴바 메뉴 에 BlockPuzzle → AI 밸런스 검수 항목 새로 생성
- 이 속성이 바로 아래에 위치한 메서드를 클릭 이벤트와 연결해줌

## 함수

### public static T GetWindow<T>() where T : EditorWindow;

- `T`: 띄우고자 하는 `EditorWindow` 상속 클래스 타입
- `title`: 창의 상단 탭에 표시될 이름

```csharp
[MenuItem("BlockPuzzle/AI 밸런스 검수")]
public static void ShowWindow() => GetWindow<BalanceReviewWindow>("AI 밸런스 검수");
```

### OnGUI

- 에디터 창이 새로고침되거나 마우스 커서가 올라가는 등 프레임 단위 이벤트가 발생할 때마다 UI 그리는 함수

### GUILayout.Button(string text)

- 함수를 호출하는 순간 화면에 버튼 UI를 그림과 동시에 클릭 여부 감지
- `if (GUILayout.Button(...))` 조건문 형태로 작성하여 클릭 시 실행할 코드를 중괄호 안에 작성

### EditorBUILayout.Helpbox(string message, MessageType type)

- 메세지를 아이콘과 함께 깔끔한 상자 형태로 표시해주는 메서드

| **MessageType 값** | **상자 형태 및 아이콘** | **주요 용도** |
| --- | --- | --- |
| **`MessageType.Info`** | 파란색 정보 `(i)` 아이콘 | 진행 상황 안내, 단순 참고 사항 알림 |
| **`MessageType.Warning`** | 노란색 경고 `(!)` 아이콘 | 주의가 필요한 상황, 미설정 항목 안내 |
| **`MessageType.Error`** | 빨간색 오류 `(X)` 아이콘 | 에셋 로드 실패, 필수 데이터 누락 등 예외 발생 시 |
| **`MessageType.None`** | 아이콘 없음 | 일반 박스 스타일의 텍스트 출력 |

### EditorGUI.DisabledScope(bool)

- 특정 범위 안의 UI 컨트롤들을 일시적으로 비활성화 상태로 만드는 클래스
- true일 경우 비활성화
- `using`이랑 같이 쓰임: Unity 에디터 전체의 UI 활성화 상태를 원상복구 만들기 위해
    - `using`: 특정 범위 안에서만 일시적으로 설정을 적용하고 범위가 끝나는 시점에 원상복구

```csharp
// _isRunning 값에 따라 "모든 미션 검수 실행"버튼의 상태가 달라짐
using (new EditorGUI.DisabledScope(_isRunning))
{
		// _isRunning이 false일 때만 버튼 클릭 가능
    if (GUILayout.Button("모든 미션 검수 실행"))
    {
        RunReviewAll();
    }
}
// using 블록을 벗어나면 별도 코드 없이도 GUI.enabled 상태로 자동 복구
// -> 다른 버튼들은 _isRunning 값이랑 관련 없음
```

### EditorGUILayout.BeginScrollView(Vector2 scrollPosition)

- 스크롤 영역의 시작을 선언하는 메서드
- scrollPosition: 현재 스크롤바의 가로/세로 위치 좌표 넘겨줌
- 반환값(Vector2): 사용자가 마우스 휠이나 드래그로 변경한 최신 스크롤 좌표 반환
- `_scroll = EditorGUILayout.BeginScrollView(_scroll);`
이렇게 매개변수, 반환값을 같이 써줘야함
안써줄 경우 새로 변경된 좌표값이 `_scroll` 변수에 저장되지 않아 스크롤바 놔버리는 순간 맨 위로 튕겨서 돌아감

### EditorGUILayout.TextArea(string text, params GUILayoutOption[] options)

- 여러 줄의 긴 텍스트를 표시하거나 편집할 수 있는 입력 상자를 생성하는 메서드
- text: 상자 안에 출력할 텍스트 문자열 전달
- options: 높이, 너비 등 레이아웃 스타일을 설정하는 옵션 전달
- 반환값(string): 사용자가 에디터 상에서 텍스트를 수정했을 때 최신으로 변경된 전체 문자열 반환
- params: 배열 문법 생략하고 낱개로 써도 컴파일러가 알아서 배열로 포장

| **GUILayoutOption 메서드 형태** | **설명** |
| --- | --- |
| **`GUILayout.ExpandHeight(bool expand)`** | 세로 남은 공간 가득 채우기 여부 지정 |
| **`GUILayout.ExpandWidth(bool expand)`** | 가로 남은 공간 가득 채우기 여부 지정 |
| **`GUILayout.Height(float height)`** | 세로 높이(px)를 고정값으로 지정 |
| **`GUILayout.Width(float width)`** | 가로 너비(px)를 고정값으로 지정 |
| **`GUILayout.MinHeight(float minHeight)`** | 최소 세로 높이 제한 지정 |
| **`GUILayout.MaxHeight(float maxHeight)`** | 최대 세로 높이 제한 지정 |
| **`GUILayout.MinWidth(float minWidth)`** | 최소 가로 너비 제한 지정 |
| **`GUILayout.MaxWidth(float maxWidth)`** | 최대 가로 너비 제한 지정 |

### EditorGUILayout.EndScrollView()

- `EditorGUILayout.BeginScrollView`로 시작된 스크롤 영역의 끝을 선언하는 메서드
- 스크롤 스코프를 닫아 영역 작성을 종료

```csharp
private void OnGUI()
{
    using (new EditorGUI.DisabledScope(_isRunning))
    {
        if (GUILayout.Button("모든 미션 검수 실행"))
        {
            RunReviewAll();
        }
    }

    if (_isRunning)
        EditorGUILayout.HelpBox("검수 진행 중...", MessageType.Info);

    _scroll = EditorGUILayout.BeginScrollView(_scroll);
    EditorGUILayout.TextArea(_resultLog, GUILayout.ExpandHeight(true));
    EditorGUILayout.EndScrollView();
}
```

### Repaint()

- 현재 에디터 창의 UI를 즉시 다시 그리도록 요청하는 메서드
- Unity의 에디터 윈도우 UI는 마우스를 움직이거나, 클릭하거나, 창 크기를 조절하는 등의 사용자 입력 이벤트가 발생할 때만 다시 그려지는 것이 기본 동작임
따라서 코드 내부에서 변수 값(예: `_resultLog`, `_isRunning`)이 변경되어도, 사용자가 마우스를 창 위에 가져가지 않으면 화면이 업데이트되지 않고 이전 상태 그대로 멈춰있는 것처럼 보임