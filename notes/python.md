### 모듈 vs 패키지
- 모듈: py 파일 하나
- 패키지: py 파일 여러 개를 묶은 폴더

### 파이썬 레이아웃
- 크게 두 가지가 있음
1. flat layout: 코드가 루트에 바로 있음
```PlainText
myproject/
    pyproject.toml
    src/
        mypackage/
            __init__.py
            foo.py
    tests/
        test_foo.py
```
2. src layout: 코드를 `src/` 라는 폴더 밑에 한 번 더 감쌈
```PlainText
myproject/
    pyproject.toml
    src/
        mypackage/
            __init__.py
            foo.py
    tests/
        test_foo.py

```

### 표준 파일 / 폴더
| 이름 | 역할 |
| --- | --- |
| `pyproject.toml` | 프로젝트 설정 파일 — 이름/버전/의존성/실행 커맨드 등 메타데이터 전부 여기 |
| `README.md` | 프로젝트 설명 |
| `tests/` | 테스트 코드 모음 (보통 실제 소스코드와 분리) |
| `docs/` | 문서 |
| `.gitignore` | git이 무시할 파일 목록 (`.venv/`, `__pycache__/` 등) |
| `uv.lock` / `poetry.lock` 등 | 의존성 버전을 정확히 고정해두는 잠금 파일 (uv/poetry 같은 도구가 자동 생성) |
| `.venv/` | 가상환경 — 이 프로젝트 전용 파이썬 + 설치된 패키지들이 들어있는 폴더 (git에는 안 올림) |

### pyproject.toml
```
[project.scripts]
어떤명령어 = "어떤모듈.경로:함수이름"
```
`python 파일경로.py`처럼 파이썬 직접 언급할 필요 없이 `어떤명령어`라고만 치면 그 함수 실행

### 실행
- python.exe - CLI에서 `python ~~` 이런 식으로 입력할 때 쓰는 `python`이 `python.exe`의 줄임말
    - C언어로 작성되어 기계어로 컴파일된 파이썬 바이트코드 실행기(인터프리터)
        - 인터프리터: 는 프로그래밍 언어로 작성된 소스 코드를 미리 컴파일하지 않고 프로그램이 실행되는 시점에 한 줄(또는 한 단위)씩 읽어서 기계 명령어로 즉시 해석하며 실행하는 번역 프로그램
    - 파이썬 소스 코드(.py)를 읽어 바이트코드(.pyc)로 변환한 뒤 파이썬 가상 머신 위에서 한 줄씩 기계 명령어로 해석하며 실행
    - .py 파일은 독자적인 실행 능력이 없으므로 모든 파이썬 프로그램 실행의 실체는 python.exe임

    | **구분** | **유니티 빌드 결과물 (Game.exe)** | **python.exe (인터프리터)** |
    | --- | --- | --- |
    | **소스 코드** | C# (IL2CPP / Mono를 통해 컴파일) | C / C++ (CPython 기준) |
    | **실행 방식** | 네이티브 기계어 직접 실행<br>(OS가 메모리에 로드하여 CPU가 바로 실행) | 인터프리터 기반 간접 실행<br>(OS는 `python.exe` 프로세스를 띄우고, `python.exe`가 파이썬 코드를 해석) |
    | **코드 포함 여부** | 게임 로직 전체가 이미 기계어/바이너리로 빌드되어 내장됨 | 프로그램 실행 엔진만 존재하며, 실행할 외부 파이썬 코드(`.py`)를 입력받아야 함 |
- 프로그램 실행하려면 python.exe에 모듈 위치와 파이썬 옵션을 직접 지정해주어야함
- 이 과정을 사용자가 매번 길게 입력하지 않도록 래핑해주는 것이 진입점(Entry Point) 스텁
    - 스텁: 진짜 프로그램(python.exe)을 대신 켜서 우리 코드를 연결해 주는 아주 작은 껍데기 파일
    ```bash
    # 1-1. 모듈 실행 옵션(-m)을 사용하는 경우
    python -m generateclassdiagram.server

    # 2-2. 인라인 코드 실행 옵션(-c)을 사용하는 경우 (스텁이 실제로 수행하는 방식)
    python -c "from generateclassdiagram.server import main; main()"
    ```
- `pyproject.toml`에 설정 두고 패키지 설치하면 파이썬 패키징 도구가 운영체제에 맞는 아주 작은 스텁 만들어줌
    ```plaintext
    [project.scripts]
    generateclassdiagram = "generateclassdiagram.server:main"
    ```
- 위처럼 세팅하고 터미널에 `generateclassdiagram` 입력할 경우 패키징 도구가 만들어준 스텁이 아래 명령 대신 수행
    ```bash
    # 지정한 모듈(generateclassdiagram.server)에서 지정한 함수(main)를 import한 뒤 바로 호출(main())
    python -c "from generateclassdiagram.server import main; main()"
    ```


