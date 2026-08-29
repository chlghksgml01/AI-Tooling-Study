## python

- 파이썬은 동적 언어임 → 변수의 데이터 타입을 코드 작성할 때 정하지 않고 프로그램이 실행되는 시점에 알아서 결정
- 위에서 아래로 읽음 → 함수나 클래스를 선언하기 전에 호출하면 에러남
- 파이썬이 이름(변수)을 찾을 때 거치는 순서는 4단계(LEGB 법칙)
    - Local (메서드 내부 지역 변수)
    - Enclosing (자신을 감싸는 상위 함수)
    - Global (파일 전역 변수)
    - Built-in (파이썬 내장 함수/키워드)

### 파싱
- 의미를 알 수 없는 문자열(텍슽트) 덩어리를 규칙에 맞춰 읽어낸 뒤 의미 있는 데이터 구조(객체, 딕셔너리 등)으로 변환하는 작업

### 파서
- 파싱 과정을 실제로 수행해 주는 프로그램, 함수, 모듈
---

### import

- 외부에서 설치한 기능(라이브러리/모듈)을 가져올 때 사용
- `from ... import ...`: 모듈 전체를 가져오는 대신 특정 폴더(패키지)나 모듈 내부의 특정 클래스, 함수, 변수만 골라서 가져올 때 사용

```python
# httpx 라이브러리/모듈 전체 가져오기
import httpx
# typing 라이브러리/모듈 에서 어떤 타입이든 허용한다는 의미의 Any 데이터 타입 가져오기
from typing import Any
```

### 튜플
- 여러 개의 데이터를 하나로 묶어서 관리하는 수정 불가한 리스트

---

## 연산자

### not

- 뒤에 오는 값의 참/거짓을 반대로 뒤집는 논리 연산자

### in

- 어떤 값이 데이터 안에 존재하는지 확인하는 연산자

### 슬라이싱 [:]

- 리스트나 문자열같은 연속된 데이터에서 처음부터 지정한 개수만큼 잘라낼 때 사용
- [시작:끝]

```python
menu = ["라면", "김밥", "떡볶이", "순대", "튀김", "오뎅"]

# 맨 처음부터 3개만 잘라오기
print(menu[:3])
# 출력: ['라면', '김밥', '떡볶이']
```

```python
text = "Hello World"

# 맨 처음부터 5글자만 잘라오기
print(text[:5])
# 출력: Hello
```

### 집합 연산자
| **기호** | **수학** | **파이썬 연산** |
| --- | --- | --- |
| **`&`** | 교집합 (AND) | `A & B` |
| **`\|`** | 합집합 (OR) | `A \| B` |
| **`-`** | 차집합 | `A - B` |
| **`^`** | 대칭차집합 | `A ^ B` |

```python
A = {1, 2, 3}
B = {3, 4, 5}

print(A & B)  # {3}       (교집합: 공통인 것)
print(A | B)  # {1,2,3,4,5} (합집합: 전부 합침)
print(A - B)  # {1, 2}    (차집합: A에서 B 차감)
print(A ^ B)  # {1, 2, 4, 5} (대칭차집합: 안 겹치는 것만)
```

---

## 구분자

### 중괄호`{}`

- 데이터 집합을 만들거나(실제 데이터 생성), 문자열 안에 변수 넣을 때 사용
- 딕셔너리 생성: Key, Value 한 쌍으로 묶어 저장 시 사용
    
    ```python
    user = {"name": "Choi", "age": 28}
    ```
    
- 세트(Set) 생성: 중복 허용하지 않는 값들의 모임 만들 때 사용
    
    ```python
    numbers = {1, 2, 3, 3, 3}  # 실제로는 {1, 2, 3}만 저장됨
    ```
    
- f-string(문자열 포매팅): 문자열 내부에서 변수나 식의 값 출력할 때 사용
    
    ```python
    name = "Choi"
    print(f"안녕하세요, {name}님!")  # 출력: 안녕하세요, Choi님!
    ```
    

### 대괄호`[]`

- 리스트 만들거나 이미 만들어진 데이터의 특정 위치/키에 접근할 때 사용
- 리스트 생성
    
    ```python
    inventory = ["potion", "sword", "shield"]
    ```
    
- 데이터 접근 및 인덱싱: 리스트, 튜플, 문자열 등에서 특정 순서 값 가져올 때 사용
    
    ```python
    inventory = ["potion", "sword", "shield"]
    print(inventory[0])  # "potion" (0번째 요소)
    ```
    
- 딕셔너리 값 접근 및 수정: 딕셔너리의 Key 로 Value를 찾거나 변경할 때 사용
    
    ```python
    user = {"name": "Choi", "age": 28}
    print(user["name"])  # "Choi"
    user["age"] = 29     # age 값 수정
    ```
    
- 타입 힌팅 세부 지정: dict, list 등 내부 상세 타입 지정
    
    ```python
    items: list[str] = ["apple", "banana"]
    data: dict[str, int] = {"score": 100}
    ```
    

---
## 컴프리헨션
| **종류** | **괄호 모양** | **특징 및 사용 목적** |
| --- | --- | --- |
| **1. 리스트 컴프리헨션** | `[ ]` | 순서가 유효하고 중복을 허용하는 리스트 생성 (가장 많이 사용) |
| **2. 셋(Set) 컴프리헨션** | `{ }` | 중복을 자동 제거하는 집합 생성 |
| **3. 딕셔너리 컴프리헨션** | `{ k: v }` | Key-Value 형태의 Map/HashTable 생성 |
| **4. 제너레이터 표현식** | `( )` | 메모리를 아끼는 스트림 형태의 반복자 생성 |

### 리스트 컴프리헨션

- 리스트를 짧고 간결하게 한 줄로 새로 만드는 문법

```python
[ 리스트에_담을_값 for 변수 in 데이터묶음 ]
```

- 리스트에_담을_값: 꺼내온 데이터를 어떻게 요리해서 리스트에 넣을지 적음
- 대괄호`[]`: 새로운 리스트를 만들겠다는 뜻
- `for 변수 in 데이터 묶음`: 기존 데이터에서 하나씩 꺼내옴’
- 기존 for 문 방식
    
    ```python
    numbers = [1, 2, 3, 4, 5]
    squares = []
    
    for x in numbers:
        squares.append(x * x) # 하나씩 제곱해서 넣기
    
    # 결과: [1, 4, 9, 16, 25]
    ```
    
- 리스트 컴프리헨션 방식
    
    ```python
    numbers = [1, 2, 3, 4, 5]
    squares = [x * x for x in numbers]
    
    # 결과: [1, 4, 9, 16, 25]
    ```
    
- 조건문까지 붙일 수 있음
    
    ```python
    numbers = [1, 2, 3, 4, 5]
    # [ 리스트에_담을_값 for 변수 in 데이터묶음  if 조건식 ]
    even_squares = [x * x for x in numbers if x % 2 == 0]
    
    # 결과: [4, 16] (2와 4만 제곱됨)
    ```
    

---

### 셋 컴프리헨션
- 중복 없는 집합(set)을 한 줄로 만들어내는 문법
```python
{ 결과물표현식 for 임시변수 in 반복대상 }
squares = {x * x for x in range(1, 6)}
# 결과: {1, 4, 9, 16, 25}

# 조건문 붙여 필터링할 수 있음
even_squares = {x * x for x in range(1, 11) if x % 2 == 0}

print(even_squares)
# 출력: {64, 4, 36, 100, 16} (순서는 해시 테이블 특성상 보장되지 않음)
```


## 표현식

### f-string 멀티라인 리터럴

- 파이썬에서 여러 줄짜리 문자열과 변수 값을 끼워 넣는 기능 합친 문법
- 멀티라인 리터럴 (`"""..."""` 또는 `'''...'''`)
    - 큰따옴표 3개를 쓰면 텍스트 안에서 `Enter`를 눌러 줄바꿈을 한 모습 그대로 문자열 만들어짐
    - `\n` 같은 줄바꿈 기호를 일일이 쓰지 않아도 됨 → 긴 글 작성할 때 가독성 뛰어남
- f-string (`f"..."`)
    - 문자열 앞에 접두사 `f`를 붙이면 중괄호 `{ }` 안에 변수명이나 파이썬 표현식을 넣어 실행 시점에 해당 값으로 치환할 수 있음

```python
name = "홍길동"
role = "개발자"
age = 28

# f-string 멀티라인 리터럴 사용
profile = f"""
[프로필 정보]
- 이름: {name}
- 직업: {role}
- 나이: {age + 1}세 (내년)
"""

print(profile)
```

- 출력

```
[프로필 정보]
- 이름: 홍길동
- 직업: 개발자
- 나이: 29세 (내년)
```

---

## 데코레이터(`@`)

- 함수 위에 `@`를 붙여서 그 함수에 특별한 기능이나 역할을 부여하는 도구
- 기존 함수를 수정하지 않고도 새로운 기능을 덧붙이거나 외부 시스템에 등록할 때 사용
### @staticmethod
- 클래스 내부에서 인스턴스(`self`) 없이 독립적으로 동작하는 정적 메서드 정의할 때 사용
```python
class Calculator:

    @staticmethod
    def add(a: int, b: int) -> int:
        return a + b

# 인스턴스 생성 없이 바로 호출 가능
result = Calculator.add(5, 3)  # 8
```
### @classmethod
- 객체 만들지 않고도 클래스 이름으로 직접 호출할 수 있는 메서드 정의
- 호출할 때 파이썬이 현재 클래스 자체를 자동으로 첫 번째 인자로 전달
- 자신 또는 자식 클래스가 아닌 다른 클래스는 인자로 주입 안됨
- 아직 객체가 생성되지 않았거나 객체 개발 데이터와 상관없이 클래스 전체 공통 기능/생성 로직 처리할 때 쓰임
    → C#의 `static`과 비슷한 위치(같진 않음)
    | **구분** | **C#** | **파이썬** | **특징** |
    | --- | --- | --- | --- |
    | **클래스 메타데이터 사용** | `static` | `@classmethod` (`cls`) | 클래스 자신(`cls`)을 넘겨받아 생성자(팩토리) 패턴 구현 시 주로 사용 |
    | **순수 유틸리티 함수** | `static` | `@staticmethod` | `self`나 `cls`를 전혀 받지 않는 순수 계산/도우미 함수 |

```python
class User:

    def __init__(self, name: str, age: int):
        self.name = name
        self.age = age

    @classmethod
    def from_birth_year(cls, name: str, birth_year: int):
        # 클래스 상태를 기반으로 인스턴스를 다르게 생성 (대체 생성자)
        age = 2026 - birth_year
        return cls(name, age)

# 생년월일로 객체 생성
user = User.from_birth_year("김철수", 1998)
print(user.age)  # 28
```
### @property
- 메서드를 변수(속성)처럼 `instance.func()`가 아닌 `instance.func` 형태로 호출할 수 있게 해주며 게터/세터(Getter/Setter) 구현에 쓰임
```python
class Rectangle:

    def __init__(self, width: float, height: float):
        self.width = width
        self.height = height

    @property
    def area(self) -> float:
        # 메서드지만 () 없이 r.area 형태로 사용
        return self.width * self.height

rect = Rectangle(5, 4)
print(rect.area)  # 20 (rect.area()가 아님)
```
### @dataclass
데이터를 저장하는 용도의 클래스를 아주 간단하게 만들 수 있게 도와주는 데코레이터, 백그라운드에서 아래 메서드 자동 생성
- \_\_init__(): 변수들을 입력받아 객체를 생성하는 초기화 메서드
- \_\_repr__(): print(obj)를 했을 때 ClassModel(name='Player', is_interface=False) 형태로 알아보기 쉽게 출력해 주는 메서드
-  \_\_eq__(): 두 객체의 값이 같은지 비교(obj1 == obj2)해 주는 메서드
```python
from dataclasses import dataclass

@dataclass
class Point:
    x: int
    y: int

p1 = Point(10, 20)
print(p1)  # Point(x=10, y=20) (__repr__ 자동 생성)
```
---

## 타입 힌팅

- 파이썬 코드에 변수나 함수가 어떤 타입의 데이터를 사용하는지 명시(힌트)해 주는 문법

```python
from typing import Any

# 1. 기본 타입 지정
user_name: str = "Choi"
user_age: int = 28
is_developer: bool = True

# 2. 리스트(List) 타입 지정: 문자열만 담기는 리스트
inventory: list[str] = ["potion", "sword", "shield"]

# 3. 딕셔너리(Dict) 타입 지정: Key는 문자열, Value는 어떤 타입이든 가능
player_info: dict[str, Any] = {
    "name": "Choi",
    "level": 10,
    "skills": ["dash", "slash"],
    "is_alive": True
}
```

- `Any`: 어떤 종류가 들어와도 상관 없다는 키워드

---

## 자료구조

### 딕셔너리

- 기본 형태: 중괄호 사용, 키와 값은 `:`로 구분, 각 요소는 `,`로 구분
- `{ ... }[self]`: 딕셔너리를 생성하자마자 바로 [ ]로 값 조회, [self]가 없다면 전체 딕셔너리 자체를 가져옴
```python
player = {"name": "Choi", "level": 10, "is_active": True}

# [self]로 함수 인자(season_name)를 키로 즉시 조회
def get_season_code(season_name):
    return {
        "spring": 1,
        "summer": 2,
        "autumn": 3,
        "winter": 4,
    }[season_name]

print(get_season_code("summer"))  # 출력: 2
```

- 자주 쓰는 조작법

| **구분** | **코드 예시** | **설명** |
| --- | --- | --- |
| **조회** | `player["name"]` | Key로 Value 가져오기 (Key가 없으면 에러 발생) |
| **안전한 조회** | `player.get("score", 0)` | Key가 없으면 에러 대신 기본값(`0`) 반환 |
| **추가 / 수정** | `player["level"] = 11` | 기존 Key가 없으면 추가, 있으면 값 수정 |
| **삭제** | `del player["is_active"]` | 해당 Key-Value 쌍 삭제 |
| **Key 존재 확인** | `"name" in player` | 해당 Key가 존재하는지 `True`/`False` 반환 |

---

## 함수

- 기본 함수 정의: `def`
- 반환값: `->`

```python
def add(a: int, b: int) -> int: # -> int 는 생략해도 됨
    result = a + b
    return result  # 결과값 반환

# 함수 호출
output = add(3, 5)  # output: 8
```

### 인자 넘겨주지 않았을 때 사용할 기본값 지정

- 기본값 없는 매개변수는 기본값 있는 매개변수보다 무조건 앞에 있어야 함

```python
def print_info(name, level: int = 1, money = 500) -> None:
    print(f"이름: {name}, 레벨: {level}")

print_info("전사") # 이름: 전사, 레벨: 1
print_info("마법사", 99, 10000) # 이름: 마법사, 레벨: 99
print_info("도적", money=3000) # 이름: 도적, 레벨: 1
```

### 가변 인자 (`*args`, `**kwargs`)

- 개수가 정해지지 않은 여러 개의 인자를 받을 때 사용
- `*args`: 여러 개의 값을 튜플(Tuple)로 받음
- `**kwargs`: 키=값 형태의 여러 인자를 딕셔너리(Dictionary)로 받음

```python
def sum_all(*numbers):
    return sum(numbers)

print(sum_all(1, 2, 3, 4, 5))  # 출력: 15
```

### 여러 개의 값 반환

```python
def get_min_max(numbers: list):
    return min(numbers), max(numbers)  # 2개의 값을 동시에 반환

minimum, maximum = get_min_max([10, 20, 5, 40])
print(minimum, maximum)  # 출력: 5 40
```

### 한 줄짜리 익명 함수(`lambda`)

- 단순한 일회성 연산이 필요할 때는 `def` 대신 `lambda`함수 활용

```python
# lambda 매개변수: 리턴값
add_lambda = lambda a, b: a + b
print(add_lambda(3, 5))  # 출력: 8
```

---

## 클래스

- 선언: `class 클래스이름:`

### 생성자

```python
# 생성자 메서드 - 기본 형태
def __init__(self):
    ...

# 멤버변수 있는 생성자 메서드(동적 타이핑)
def __init__(self, name, level):
    self.name = name
    self.level = level

# 멤버변수 있는 생성자 메서드(타입 표시)
# -> None: void
def __init__(self, name: str, level: int) -> None:
    self.name: str = name
    self.level: int = level
```

### 멤버변수

- 클래스 내부에서 멤버변수 사용할땐 무조건 앞에 self를 써야함
- 생성자 안에서 멤버변수 선언하는게 표준임(문법적으로 필수는 아님)
1. 생성자 없이 멤버변수 만들기 - 일반 메서드 내부에서 생성
    
    ```python
    class Character:
        def set_info(self, name:str):
            self.name = name # 호출되는 시점에 멤버 변수 생성
    player = Character()
    # print(player.name)  # ⚠️ 에러 발생! (AttributeError: 아직 name 변수가 존재하지 않음)
    player.set_info("전사")  # 이 메서드를 호출해야만 name 변수가 생성됨
    print(player.name)  # 출력: 전사
    ```
    
2. 생성자 없이 멤버변수 만들기 - 클래스 변수 형태로 선언
    
    ```python
    class Character:
        name: str = "기본이름"  # 클래스 변수 선언
    ```
    

### 클래스 함수

- 무조건 `self`를 첫 번째 인자로 가져가야함

```python
class Character:
    def move(self):  #  self 필수
        print("이동합니다.")
```

- 정적 메서드(`@staticmethod`), 클래스 메서드(`@classmethod`)에서는 self 필요없음

### Enum

```python
from enum import Enum

# Enum 클래스 정의
class Fruit(Enum):
    APPLE = "public"
    BANANA = "private"

# 사용 방법
fruit = Fruit.APPLE

print(fruit)  # Fruit.APPLE
print(fruit.name)  # 'APPLE'
print(fruit.value)  # 'public'

```

---

## 자료구조
### list
- 기본 생성 및 형태
```python
# 빈 리스트
empty_list = []

# 다양한 데이터 타입을 담은 리스트
numbers = [1, 2, 3, 4, 5]
fruits = ["apple", "banana", "cherry"]
mixed = [1, "Hello", True, 3.14]  # 여러 타입을 섞어서 저장 가능
```

- `list`, `dict`, `set` 같은 가변(Mutable) 객체는 기본값을 딱 한 번만 평가하여 메모리에 할당해 두기 때문에 객체간 메모리 공유 버그를 방지하기 위해 다음과 같이 선언
    `items: list[str] = field(default_factory=list)`
    - dataclasses 모듈에서 제공하는 안전한 기본값 생성 방식
    - 파이썬은 클래스가 정의될 때 기본값으로 지정된 `[]`(리스트)를 딱 한 번만 생성하여 모든 인스턴스가 공유
    - 새로운 객체(인스턴스)가 만들어질 때마다 `list()` 함수(생성자)를 호출하여 각 객체만의 독립된 빈 리스트 메모리를 할당해 주라는 뜻
```python
from dataclasses import dataclass, field

@dataclass
class Example:
    # 객체 생성 시마다 독립된 새 리스트 생성
    items: list[str] = field(default_factory=list)
    # items: list[Field] = None 이렇게 할 경우 items에는 None이 들어가고
    # 이 상태에서 파싱한 필드를 추가하려고 append()를 호출하는 순간 터짐    
```

---
## 반복문
- 주의: ++, -- 증감 연산자가 없음
### for
- C#의 `foreach` 문처럼 동작

```python
# 기본 사용
items = ["인벤토리1", "인벤토리2", "인벤토리3"]
for item in items:
    print(item)

# 0 ~ N-1까지 반복(range(N))
for i in range(5): # 0, 1, 2, 3, 4 (총 5번 반복)
    print(i)

# 범위 지정 반복 (range(start, stop, step))
for i in range(1, 10, 2): # 1부터 10 미만까지 2씩 증가 (1, 3, 5, 7, 9)
    print(i)

# for - else 문 존재
numbers = [1, 3, 5, 7]

for num in numbers:
    if num % 2 == 0:
        print("짝수 발견")
        break
else:
    # break를 안 만나고 끝까지 돌았을 때 실행됨
    print("짝수 발견 실패")
```

### while

```python
count = 0
while count < 3:
    print(f"카운트: {count}")
    count += 1 
```

### 반복문 제어
- C#이랑 똑같음

```python
numbers = [1, 2, 3, 4, 5]

for num in numbers:
    if num == 3:
        continue  # 3일 때는 출력하지 않고 건너뜀
    if num == 5:
        break     # 5를 만나면 반복문 종료
    print(num)    # 출력 결과: 1, 2, 4
```

---
## 키워드

### `with [자원_생성_코드] as [변수명]`

- 쓰고 나서 반드시 닫아야(해제해야)하는 자원을 안전하고 깔끔하게 관리할 때 사용하는 구문
- `자원_생성_코드`: 파일 열기(`open()`), 네트워크 세션 생성 등 사용 후 정리 작업이 꼭 필요한 객체 만듦
- `변수명`: 생성된 자원 객체를 블록 안에서 쓸 수 있도록 변수에 할당
- `with` 안 썼을 때
    
    ```python
    f = open("file.txt", "w")
    f.write("Hello World")
    # ... 만약 여기서 에러가 발생하면 f.close()가 실행되지 않아 파일이 안 닫히고 메모리에 남음
    f.close()
    ```
    
- `with` 썼을 때
    
    ```python
    with open("file.txt", "w") as f:
        f.write("Hello World")
    
    # with 블록을 벗어나는 순간, 에러가 나든 안 나든 알아서 f.close()를 호출해 파일이 닫힘
    ```
-  with 뒤에 놓여서 with의 자동 정리 메커니즘(`__enter__`, `__exit__`)을 받아낼 수 있는 모든 것: 컨텍스트 매니저
    - 리소스의 획득(setup)과 정리(teardown)를 한 쌍으로 묶어서 보장해주는 객체
    - `with` 블록에 들어갈 때 준비 작업을, 나갈 때 정리 작업을 자동으로 실행
    - 중간에 예외가 발생해도 정리 코드는 반드시 실행


### open
- 파일을 읽거나 쓰기 위해 파일 객체를 열어주는 함수
- `with` 구문과 함꼐 사용하는 것이 표준 방식
- 기본 구조
    ```python
    with open("파일경로/파일명.ext", "모드", encoding="인코딩방식") as 변수명:
        # 파일 가지고 할 작업 코드 작성
    ```
    - 파이썬에서 `\` 는 특수 문자라 경로 적을 때 문제생김 아래 해결방안
        ```python
        open(r"C:\UnityProjects\BlockPuzzle\...\MissionSummaryExtractor.cs`)   # 앞에 r  ← 이게 제일 쉬움
        open("C:/UnityProjects/BlockPuzzle/.../MissionSummaryExtractor.cs")    # / 로 바꾸기
        open("C:\\UnityProjects\\BlockPuzzle\\...")                            # \ 두 번씩
        ```
    - `open(...)`: 지정한 파일과 통신할 수 있는 파일 객체 생성
    - `as 변수명`: 열린 파일 객체를 코드 안에서 사용할 변수로 할당(보통 f, file 등 사용)
    - `with ...`: 이 블록을 벗어나는 순간(정상 종료든 오류 발생이든) `f.close()` 자동으로 호출
    - 모드

        | **모드** | **이름** | **설명** |
        | --- | --- | --- |
        | **`"r"`** | Read (읽기) | 파일 내용만 읽음(기본값)<br>파일이 없으면 `FileNotFoundError` 발생 |
        | **`"w"`** | Write (쓰기) | 파일에 덮어씀 파일이<br>이미 있으면 기존 내용을 싹 지우고 새로 만듦 |
        | **`"a"`** | Append (추가) | 파일의 맨 끝에 내용을 덧붙임<br>기존 내용은 유지 |
        | **`"x"`** | Exclusive (생성) | 파일을 새로 만듦<br>이미 동일한 파일이 있다면 에러 발생 |
        | **`"b"`** | Binary (바이너리) | 텍스트가 아닌 이미지, 음성, 엑셀 등의 바이너리 파일용 (`"rb"`, `"wb"` 형태) |
    - 인코딩
        - encoding="utf-8": 표준적인 글로벌 인코딩(가장 권장)
        - encoding="cp949" / "euc-kr": 윈도우 환경이나 메모장에서 한글이 깨질 때 주로 사용
- with 구문이 자동으로 닫아줄 수 있는 이유: `open()`이 반환하는 파일 객체가 파이썬의 컨텍스트 매니저 규격을 따르기 때문
    ```Plaintext
    1. with 문 진입 ──> 객체의 __enter__() 실행 (파일 open)
    2. with 블록 내부 코드 실행
    3. with 문 탈출 ──> 객체의 __exit__() 실행 (자동으로 f.close() 실행)
    ```

### read
- 파일 전체 내용을 하나의 연속된 문자열 또는 바이너리 데이터로 읽어오는 함수
    ```python
    파일객체.read(size=-1)
    ```
    - `size`: 읽어올 글자 수(또는 바이트수), 생략하거나 음수 넣으면 파일 전체 읽어옴
        그 글자 수(바이너리 모드에서는 바이트 수)만큼만 읽고 멈춤
        연속해서 호출하면 읽은 부분 다음부터 이어 읽음

### async / await

- `async`: 작업 끝날 때까지 기다리지 않고 기다리는 동안 다른 일 처리하겠다는 키워드
- `await`: 기다리는 동안 다른 비동기 작업 수행할 수 있게 제어권 양보, async def 함수 안에서만 사용 가능
1. 비동기 함수
    - 비동기 함수 내부에서는 블로킹(동기) 라이브러리 쓰면 안됨
    - `async def` 내부에서 비동기 전용 통신 라이브러리(`httpx`, `aiohttp`)나 비동기 대기(`asyncio.sleep()`)를 쓰지 않고, 일반 `requests.get()`이나 `time.sleep()`을 쓰면 프로그램 전체가 다시 멈춰버림
    
    ```python
    import asyncio
    import time
    
    async def download_file(name, delay):
        print(f"{name} 다운로드 시작...")
        await asyncio.sleep(delay)  # 서버 응답 대기 시간을 가정
        print(f"[{name}] 다운로드 완료! ({delay}초 소요)")
    
    async def main():
        start = time.time()
    
        # 3개의 비동기 작업을 동시에(병렬로) 실행
        await asyncio.gather(
            download_file("파일 A", 2),
            download_file("파일 B", 3),
            download_file("파일 C", 1),
        )
    
        print(f"전체 소요 시간: {time.time() - start:.2f}초")
    
    asyncio.run(main())
    ```
    
    - 실행 결과 (C는 1초, A는 2초, B는 3초라서 완료 순서가 뒤바뀜 — 3개가 동시에 대기하므로 전체 3초)
    
    ```
    파일 A 다운로드 시작...
    파일 B 다운로드 시작...
    파일 C 다운로드 시작...
    [파일 C] 다운로드 완료! (1초 소요)
    [파일 A] 다운로드 완료! (2초 소요)
    [파일 B] 다운로드 완료! (3초 소요)
    전체 소요 시간: 3.01초
    ```
    
    - `async def`로 선언된 함수(코루틴)를 실행하고 그 결과값을 얻으려면 반드시 `await`를 붙여야 함
        
        ```python
        async def get_data():
            return "날씨 데이터"
        
        async def main():
            #  await를 안 붙인 경우
            result = get_data()
            print(result) 
            # 출력: <coroutine object get_data at 0x...> (실제 데이터가 안 나옴!)
        
            #  await를 붙인 경우
            result = await get_data()
            print(result) 
            # 출력: 날씨 데이터
        ```
        
2. 비동기 구문 안에서의 확장 사용
    - 비동기 함수 내부에서 비동기용 반복문이나 자원 관리를 할 때 각 키워드 앞에 붙음
    - **`async with`**: 비동기로 자원을 열고 닫을 때 (예: 비동기 웹 요청 세션 관리)
        
        ```python
        async with aiohttp.ClientSession() as session:
            async with session.get(url) as response:
                return await response.json()
        ```
        
    - **`async for`**: 비동기 스트림 데이터를 하나씩 반복해서 꺼내올 때
        
        ```python
        async for data in async_stream_generator():
            print(data)
        ```
        

### `**변수명` (가변 키워드 인자)

- 키워드 인자(Key=Value 형태)를 개수 제한 없이 가변적으로 받겠다는 의미의 문법
- 관례로 `**kwargs` 라고 많이 사용하지만 별 두개만 붙어있다면 `**options`, `**data` 처럼 바꿔써도 됨

```python
def print_user_info(**kwargs):
    # kwargs는 내부적으로 {"name": "홍길동", "age": 25} 형태의 딕셔너리가 됩니다.
    print(type(kwargs))  # <class 'dict'>
    
    for key, value in kwargs.items():
        print(f"{key}: {value}")

# 인자의 개수를 마음대로 조절해서 호출 가능!
print_user_info(name="홍길동", age=25, job="개발자")
```

- 실행 결과

```
<class 'dict'>
name: 홍길동
age: 25
job: 개발자
```

### join

- 문자열 접착제

```python
numbers = ["1", "2", "3"]
result = "-".join(numbers) # 결과: "1-2-3"
```

### lower
- 모든 대문자 소문자로
```python
user_input = "User@Example.com"
clean_email = user_input.lower()

print(clean_email)  # "user@example.com"
```

## 라이브러리
### re
- 정규 표현식을 다루기 위한 표준 라이브러리
```python
import re

text = "Contact: user1@test.com and user2@abc.org"

# re.search() - 전체에서 '첫 번째' 일치 항목 찾기
# Match 객체 반환
search_res = re.search(r"\w+@\w+\.\w+", text)
print(search_res.group())  # 'user1@test.com'

# re.match() - 문자열 '시작점'부터 일치하는지 확인
match_res = re.match(r"Contact", text)
print(match_res.group())   # 'Contact' (시작이 다르면 None)

# re.findall() - 일치하는 모든 항목을 리스트로 반환
# ()가 하나라도 들어가면 전체 문장이 아니라 괄호로 감싸진 부분들만 묶어서 반환
findall_res = re.findall(r"\w+@\w+\.\w+", text)
print(findall_res)         # ['user1@test.com', 'user2@abc.org']

# re.finditer() - 일치하는 모든 항목을 Match 객체 이터레이터로 반환
for m in re.finditer(r"\w+@\w+\.\w+", text):
    print(m.group(), m.span()) 
    # 'user1@test.com' (9, 23)
    # 'user2@abc.org' (28, 41)

# re.sub() - 패턴에 해당하는 부분을 다른 문자열로 치환
sub_res = re.sub(r"\w+@\w+\.\w+", "[REDACTED]", text)
print(sub_res)             # 'Contact: [REDACTED] and [REDACTED]'

# re.split() - 패턴 기준으로 문자열 분할
split_res = re.split(r"\s+and\s+|:\s+", text)
print(split_res)           # ['Contact', 'user1@test.com', 'user2@abc.org']

# re.compile() - 정규표현식 패턴을 파이썬이 빠르게 해석할 수 있는 전용 검색엔진(기계어 형태)으로 미리 만들어두는 과정
# 이게 없을 경우 매번 정규식 패턴 문자열을 해석하는 과정을 거쳐야함
pattern = re.compile(r"\w+@\w+\.\w+")
print(pattern.findall(text)) # ['user1@test.com', 'user2@abc.org']
```
- Match 객체: 정규표현식으로 문장을 찾았을 때 어디서 무엇을 발견했는지에 대한 상세한 정보를 담고 있는 결과 상자

- 문자 클래스 기호
    | **기호** | **의미** | **예시 (매칭되는 결과)** |
    | --- | --- | --- |
    | **`\b`** | 단어 경계 | `공백/특수문자cat공백/특수문자` → `cat` (매칭 O) `category` 안의 `cat` (매칭 X) | 
    | **`\d`** | 숫자 1개 (`0-9`) | `\d\d` → `24` |
    | **`\D`** | 숫자가 **아닌** 글자 1개 | `\D` → `A`, `!`, `가` |
    | **`\w`** | 문장 구성 글자 1개 (알파벳, 숫자, `_`, 언더바) | `\w` → `a`, `7`, `_` |
    | **`\W`** | `\w`가 **아닌** 글자 1개 (특수문자, 공백 등) | `\W` → `@`, `#`,  |
    | **`\s`** | 공백 문자 1개 (띄어쓰기, 탭, 줄바꿈) | `\s` →  ``(스페이스), `\n`(줄바꿈) |
    | **`\S`** | 공백이 **아닌** 글자 1개 | `\S` → `A`, `1`, `!` |
    | **`.`** | **아무 글자**나 1개 (줄바꿈 제외) | `a.c` → `abc`, `a1c`, `a!c` |
    ```python
    print(re.findall(r'\bcat\b', "cat category"))   # ['cat'] -> 단어 경계로 독립된 cat만 매칭
    print(re.findall(r'\d\d', "1a23b456"))          # ['23', '45'] -> 연속된 숫자 2자리 매칭
    print(re.findall(r'\D', "A1!"))                 # ['A', '!'] -> 숫자가 아닌 문자 매칭
    print(re.findall(r'\w', "a7_!"))                # ['a', '7', '_'] -> 알파벳, 숫자, 언더바만 매칭
    print(re.findall(r'\W', "a7_! "))               # ['!', ' '] -> \w가 아닌 특수문자, 공백 매칭
    print(re.findall(r'\s', "A B\nC"))              # [' ', '\n'] -> 스페이스, 줄바꿈 등 공백 매칭
    print(re.findall(r'\S', "A B\nC"))              # ['A', 'B', 'C'] -> 공백이 아닌 문자 매칭
    print(re.findall(r'a.c', "abc a1c a!c ac"))     # ['abc', 'a1c', 'a!c'] -> a와 c 사이 아무 문자 1개 매칭
    ```
- 수량자(반복 횟수 지정)
    | **기호** | **의미** | **예시 (매칭되는 결과)** |
    | --- | --- | --- |
    | **`*`** | 0회 이상 연속 반복 (없어도 됨) | `a*` → (빈값), `a`, `aa`, `aaa` |
    | **`+`** | 1회 이상 연속 반복 (최소 1개 필요) | `a+` → `a`, `aa`, `aaa` |
    | **`?`** | 0회 또는 1회 (있거나 없거나) | `https?` → `http`, `https` |
    | **`{n}`** | 정확히 n번 반복 | `\d{3}` → `010` (숫자 3자리) |
    | **`{n,m}`** | n번 이상 m번 이하 반복 | `\d{2,4}` → `24`, `100`, `2026` |
    | **`{n,}`** | n번 이상 반복 | `\d{3,}` → 3자리 이상의 모든 숫자 |
    ```python
    print(re.findall(r'go*l', "gl gol gool"))        # ['gl', 'gol', 'gool'] -> o가 0개 이상 반복 매칭
    print(re.findall(r'go+l', "gl gol gool"))        # ['gol', 'gool'] -> o가 최소 1개 이상 반복 매칭
    print(re.findall(r'https?', "http https"))      # ['http', 'https'] -> s가 없거나 1개 매칭
    print(re.findall(r'\d{3}', "1 12 123 1234"))     # ['123', '123'] -> 숫자가 정확히 3자리 매칭
    print(re.findall(r'\d{2,4}', "1 12 123 12345"))  # ['12', '123', '1234'] -> 숫자가 2~4자리 매칭
    print(re.findall(r'\d{3,}', "12 123 12345"))     # ['123', '12345'] -> 숫자가 3자리 이상 연속 매칭
    ```
- 예시: `\b(class|interface|struct|enum)\s+(\w+)`
    - 단어 시작 경계(`\b`)에서 `class`, `interface`, `struct`, `enum` 중 하나로 시작하고, 공백이 1개 이상 이어지며(`\s+`), 그 뒤의 타입 이름(`(\w+)`)까지 순서대로 추출하는 정규식
    - `\b`: 출발점 경계
        여기서부터 단어가 시작하는 지점이어야 함(예: myclass처럼 앞에 다른 글자가 붙어있으면 여기서 탈락)
    - `(class|interface|struct|enum)`: 키워드 확인
        단어 시작점 바로 뒤에 `class`, `interface`, `struct`, `enum` 중 하나가 와야 함
    - `\s+`: 공백 거치기
        키워드 바로 뒤에는 스페이스(공백)가 최소 1개 이상 떨어져 있어야 함
    - `(\w+)`: 이름 가져오기
        공백 바로 뒤에 나오는 알파벳/숫자/언더바(_) 연속 덩어리를 클래스(타입) 이름으로 잡음
### Path.pathlib
- 파일 시스템 경로를 객체 지향적으로 다룰 수 있게 해주는 라이브러리
- 경로를 단순 문자열이 아닌 `Path` 객체로 다룸
    - Path 객체는 `.read_text()` 메서드가 있어서 `open()` 안 쓰고 바로 읽을 수 있음
```python
from pathlib import Path

# 경로 객체 생성
p = Path("example_dir/sample.txt")
current_dir = Path.cwd()  # 현재 작업 디렉토리

# 경로 결합 (/ 연산자 사용)
new_path = current_dir / "folder" / "file.txt"

# 경로 정보 추출
print(p.name)       # sample.txt (파일명 전체)
print(p.stem)       # sample (확장자 제외 파일명)
print(p.suffix)     # .txt (확장자)
print(p.parent)     # example_dir (상위 디렉토리)

# 파일/디렉토리 존재 및 타입 확인
print(p.exists())   # 존재 여부 (True/False)
print(p.is_file())  # 파일 여부
print(p.is_dir())   # 디렉토리 여부

# 파일 읽기 및 쓰기 (open 없이 바로 처리 가능)
p_txt = Path("test.txt")
p_txt.write_text("Hello, Pathlib!", encoding="utf-8") # 파일 쓰기
content = p_txt.read_text(encoding="utf-8")           # 파일 읽기 -> 문자열 데이터로 저장

# 디렉토리 생성 및 탐색
dir_path = Path("new_folder")
dir_path.mkdir(parents=True, exist_ok=True) # 디렉토리 생성

# 특정 확장자 파일 검색 (glob: 현재 폴더만)
for file in Path(".").glob("*.py"):
    print(file)

# 재귀적 파일 탐색 (rglob: 모든 하위 폴더까지 탐색)
# 파일 경로들을 순서대로 꺼낼 수 있는 상태로 가져옴 -> 관리하고싶다면 list로 감싸기
for file in Path(".").rglob("*.py"):
    print(file)

# 경로 수정 (실제 파일은 변경되지 않고 새로운 Path 객체만 생성)
new_name_path = p.with_name("new_sample.txt")  # 파일명 변경 경로
new_ext_path = p.with_suffix(".csv")            # 확장자 변경 경로

# 절대 경로 전환
abs_path = p.resolve()  # 상대 경로를 절대 경로로 변환

# 파일 이동/이름 변경 및 삭제
# p_txt.rename("new_test.txt")    # 파일 이름 변경 또는 이동
p_txt.unlink(missing_ok=True)     # 파일 삭제 (없어도 에러 안 남)
# dir_path.rmdir()                # 빈 디렉토리 삭제
```