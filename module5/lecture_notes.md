# 📔 모듈 5: Python & FastAPI 기반 AI 서빙 및 RAG 시스템 구축 실무 기록

LMS(학습 관리 시스템)의 AI 학습 도우미 구축을 목적으로 Python의 기초 문법부터 FastAPI 기반 경량 웹 서버 구축, Hugging Face 기반 AI 모델 서빙, ChromaDB 중심의 VectorDB 연동, LangChain 기반 RAG(검색 증강 생성) 파이프라인, 그리고 Spring Boot와 FastAPI 간 Function Calling 통합 아키텍처를 실무 학습한 기록입니다.

---

## 🐍 Python & FastAPI 기초 및 서버 구축

### 1. Python 프로그래밍 특징 및 핵심 문법

* **언어적 특성 및 런타임 타입 결정**
  * Python은 동적 타입 언어로, 변수 선언 시 타입을 명시하지 않고 런타임 시점에 할당된 값을 보고 타입을 자동 판단합니다 (`type()` 메서드로 확인 가능).
  * **Java와의 비교**:
    * 변수명 규칙: Java는 `camelCase`를 사용하나 Python은 `snake_case`를 주로 사용합니다 (예: `user_name`).
    * 구역 구분: Java는 중괄호 `{}`로 구역을 지정하나, Python은 **콜론(`:`)과 들여쓰기(Tab/Indent)**로 구역을 구분하므로 들여쓰기 미준수 시 오동작합니다.
    * 값이 없음의 표현: Java의 `null` 대신 Python은 `None`을 사용합니다.
    * f-string: `f"{name}의 질문: {user_question}"` 형태로 `+` 연산자 없이 간편하게 문자열을 조합할 수 있습니다.

* **핵심 자료구조**
  * **Dictionary (`dict`)**: Java의 `Map<String, Object>` 또는 JSON 구조(`{key: value}`)와 대응되며, 순서가 없고 Key-Value 쌍으로 관리됩니다.
    * 조회: `user["name"]` 또는 `user.get("age")`
    * 수정/삭제: `user["name"] = "value"`, `del user["age"]`
  * **List (`list`)**: Java의 `ArrayList`와 대응되는 구조이며, `[]`를 사용합니다.
    * Java 배열과 달리 요소들의 자료형이 서로 달라도 저장 가능합니다.
    * 데이터 추가/삭제: `append()` 메서드로 추가(Java의 `add()`), `remove(값)` 또는 `pop(index)`로 삭제합니다.
    * 크기 확인 및 반복문: `len(list)` 함수를 사용하며, `for item in list:` 형태로 Java의 향상된 for문처럼 순회합니다.

* **Function & Class**
  * **Function**: Java의 Method는 Class 내부에서만 작성 가능하지만, Python은 함수 자체가 독립된 객체이므로 별도 클래스 없이 `def` 키워드로 작성할 수 있습니다.
    * Type-Hint: `def greet(name: str) -> str:` 형태로 개발자 간 협업 시 타입을 명시하는 힌트를 제공합니다 (런타임 제약은 없음).
    * 기본값 지정: 매개변수에 기본값을 선언하여 인자가 전달되지 않을 때 사용 가능합니다.
  * **Class**: 관련 있는 함수와 변수를 하나로 묶는 구조입니다.
    * `__init__(self)`: Java의 생성자 역할을 수행합니다.
    * `self`: Java의 `this`와 동일하며, 객체 자기 자신의 레퍼런스 주소를 가리킵니다.

* **Import 및 Exception Handling**
  * **Import**: `from 파일명 import 클래스명/함수명` 구조를 사용하여 외부 라이브러리 및 타 모듈의 기능을 호출합니다.
  * **Exception Handling**:
    * Java의 `try-catch` 및 `throw` 구문에 대응하여 Python은 **`try-except`** 및 **`raise`** 구문을 사용합니다.
    * AI 서버 처리 중 자주 발생하는 예외: 질문 누락, 만료된 모델, API Key 만료/초과, RAG 파일 I/O 실패(`FileNotFound`).

* **File I/O**
  * **Stream 자원 관리**: Java의 `try-with-resources`와 동일하게 Python에서는 **`with open("파일명", "모드", encoding="utf-8") as file:`** 문법을 사용하여 파일 조작 후 Stream을 자동 닫기 처리합니다.
  * 파일 모드: `"r"` (읽기), `"w"` (덮어쓰기), `"a"` (추가/누적 - 개행시 `\n` 필요).
  * 주요 메서드: `readlines()` (파일 내용을 리스트 형태로 분할하여 읽기), `read().strip()` (전체 통문자열 읽기 및 공백 제거).

---

### 2. FastAPI 기반 경량 웹 서버 구축

* **FastAPI 개요 및 실행 구조**
  * Python 기반의 경량/고성능 API 서버 프레임워크로, Spring Boot와 동일한 웹 서버 역할을 담당합니다.
  * 내장 Swagger 문서를 자동 제공하며(`http://localhost:8000/docs`), Pydantic 기반의 검증과 비동기 처리를 지원합니다.
  * **구동 인프라**: Spring Boot가 내장 Tomcat(8080)을 사용하듯, FastAPI는 ASGI 서버인 **Uvicorn(8000)**을 통해 실행됩니다 (`uvicorn main:app --reload`).

* **요청 처리 방식 및 Pydantic Validation**
  * **요청 전달 방식**: Query Parameter (`?key=value`), PathVariable (`/{id}`), RequestBody (`POST`).
  * **Pydantic Schema (DTO + Validation)**:
    * `BaseModel`을 상속받아 DTO를 구현하며, 자동 타입 검증을 수행합니다.
    * `Field()` 및 제약 조건 키워드(`ge`: 크거나 같음, `le`: 작거나 같음, `gt`: 초과, `lt`: 미만)를 결합하여 Spring의 `@Valid`와 동일한 매개변수 유효성 검증을 적용합니다 (검증 실패 시 `422 Unprocessable Entity` 반환).

* **Router 기반 계층화 및 패키지 구조**
  * `main.py`에 집중된 코드를 분리하기 위해 **`APIRouter`**를 도입하여 Spring의 `@Controller`처럼 역할별 모듈을 분리합니다.
  * Python 3.X 버전에서 폴더를 단순 디렉토리가 아닌 Python Package로 인식시키기 위해 각 폴더에 **`__init__.py`** 파일을 배치합니다.
  * **전체 아키텍처 구조**:
    * `main.py`: FastAPI 객체 생성, 전역 LifeSpan 관리, 라우터 등록
    * `app/api/`: APIRouter 기반 컨트롤러 (`chat_router.py`, `rag_router.py`)
    * `app/schemas/`: Pydantic 기반 Req/Res DTO (`chat_schema.py`)
    * `app/service/`: 비즈니스 로직 처리 계층 (`ai_service.py`)
    * `config.py`: `.env` 파일의 외부 API Key 및 환경 변수를 `python-dotenv`로 파싱하여 관리

---

## 🤗 Hugging Face 기반 AI 모델 서빙 및 문서 분석

### 1. Hugging Face 및 Transformers 파이프라인

* **Hugging Face 플랫폼 개요**
  * AI 모델, 데이터셋(DataSets), 데모 공간(Spaces)을 제공하는 AI 생태계의 GitHub 역할을 수행합니다.
  * 백엔드 개발 관점: AI 모델을 직접 설계/학습시키는 것이 아니라, 이미 사전 학습된(Pre-trained) 오픈소스 모델을 가져와 서비스 요구사항에 맞춰 **AI 서빙(Serving)**하는 것이 핵심입니다.

* **Transformers 라이브러리 및 핵심 작동 파이프라인**
  * `pipeline()` 함수: 입력 데이터 → Tokenizer(텍스트를 토큰화 및 숫자로 치환) → Transformer Model(추론) → 후처리 과정 전체를 하나로 묶어 실행하는 표준 인터페이스를 제공합니다.
  * **감정 분석(Sentiment Analysis)**:
    * `pipeline(task="sentiment-analysis", model=MODEL_ID, device=device)` 형태로 정의하여 입력 텍스트의 긍정/부정 판단 및 점수(`score`)를 도출합니다.
    * GPU 사용 가능 여부 판별: `torch.cuda.is_available()` 결과에 따라 디바이스(GPU: `0`, CPU: `-1`)를 동적으로 할당합니다.

---

### 2. PDF 문서 분석 시스템 구현

* **PDF 텍스트 추출 및 유효성 검사 (`document_loader.py`)**
  * `PyMuPDF` (`fitz`) 및 `BytesIO`를 사용하여 업로드된 PDF 파일의 메모리 스트림을 읽고 페이지별 텍스트를 추출합니다.
  * 파일 크기 제약(10MB 미만), MIME 타입 검사, 최소 추출 글자 수 검사를 통해 무효한 문서를 사전에 차단합니다.

* **지연 로딩(Lazy Loading) 및 Thread Lock 기반 모델 관리 (`services.py`)**
  * **동기**: 분류, 개체명 추출(NER), 한국어 요약 등 3개 모델을 서버 시작 시점에 모두 메모리에 올리지 않고, 기능이 **최초 호출되는 시점에 1회만 로딩**하여 서버 기동 속도 및 메모리를 최적화합니다.
  * **Double-Checked Locking 패턴**:
    * 여러 동시 요청이 들어올 때 동일 모델이 중복 로딩되는 현상을 막기 위해 `threading.Lock()`을 활용합니다.
    * Lock 획득 전후로 모델의 `None` 여부를 2번 검사하여 불필요한 Lock 대기 비용을 줄이고 안전한 지연 로딩을 구현합니다.

* **FastAPI Server LifeSpan 및 전역 서비스 관리 (`main.py`)**
  * `@asynccontextmanager` 기반의 `lifespan` 함수를 구현하여 서버 시작시 전역 서비스 객체를 준비하고, `yield` 이후 서버 종료 시점에 객체를 `None`으로 정리하는 생명주기 관리 방식을 적용합니다 (Spring의 `@PostConstruct` 및 콩 관리 구조와 대조).

---

## 🔍 VectorDB 및 LangChain 기반 RAG 시스템

### 1. 임베딩(Embedding) 및 Vector DB (ChromaDB)

* **의미 기반 검색의 필요성 (LIKE 검색의 한계 극복)**
  * 기존 SQL의 `LIKE '%자바%'` 구문은 단순 단어 일치만 검사하므로 오타/영어 표현을 놓치는 **False Negative**("Java 문법" 미검색) 문제와, 글자는 겹치나 의미가 다른 **False Positive**("자바스크립트" 잘못 검색) 문제를 발생시킵니다.
  * **임베딩(Embedding)**: 문장을 고차원의 "의미 좌표"(숫자 벡터)로 변환하는 기술입니다.
  * **코사인 유사도(Cosine Similarity)**:
    * 두 벡터가 이루는 각도를 계산하며, 1에 가까울수록 의미가 유사함을 나타냅니다.
    * 공식: $\text{Cosine Similarity} = \frac{A \cdot B}{\|A\| \|B\|}$ (`np.dot`과 `np.linalg.norm` 연산 활용).
    * 한국어 특화 모델(`jhgan/ko-sroberta-multitask`)을 주입하여 의미적 유사도를 정량 계산합니다.

* **Vector DB (ChromaDB) 연동 및 구조 대조**

| RDB (MySQL) 개념 | Vector DB (ChromaDB) 개념 |
| :--- | :--- |
| 테이블 (Table) | **컬렉션 (Collection)** |
| 기본키 (PK) | **id** |
| 일반 컬럼 | **metadata** (부가 정보, 메타 필터링용) |
| `WHERE category = '백엔드'` | `where={"category": "백엔드"}` |
| `LIKE '%단어%'` (글자 매칭) | `query()` (의미 기반 매칭) |

* **ChromaDB 운영 및 강좌 추천**
  * `PersistentClient`: 데이터를 로컬 디스크에 영속 저장하여 재가동시에도 유지됩니다.
  * `upsert`: 고유 ID 기반으로 기존 데이터는 갱신, 신규 데이터는 삽입 처리하여 중복 저장을 방지합니다.
  * 거리-유사도 관계: ChromaDB는 거리(Distance)를 반환하므로 $\text{Similarity} = 1 - \text{Distance}$ 관계가 성립합니다.
  * 강좌 추천 기법: 수강 완료한 강좌의 "제목-설명" 문서 자체를 검색 쿼리로 사용하여 유사도가 가장 높은 상위 강좌를 반환하되, 자기 자신(1등)은 제외하는 방식으로 구성합니다.

* **PDF 문맥 청킹(Chunking)**
  * 긴 문서를 통째로 임베딩하면 여러 주제가 섞여 유사도 품질이 저하되므로, 일정 단위로 자르는 **청킹(Chunking)** 작업이 필수적입니다.
  * **Chunk Overlap (겹침 구간)**: `CHUNK_SIZE=500`, `CHUNK_OVERLAP=50` 설정을 적용하여 문장 경계에서 핵심 내용이 반토막 나서 검색 대상에서 누락되는 현상을 방지합니다.
  * 메타데이터 바인딩: 각 청크에 출처 페이지 번호(`page`)를 함께 보관하여 AI 답변 생성 시 출처를 제시하도록 설계합니다.

---

### 2. LangChain 및 Gemini 기반 RAG 파이프라인

* **RAG (Retrieval-Augmented Generation) 핵심 개념 및 프롬프트 설계**
  * 모델 재학습 없이 최신/사내 데이터에 기반하여 답변하도록 **검색(Retrieval) → 증강(Augmented) → 생성(Generation)** 단계를 거치는 기법입니다.
  * **환각(Hallucination) 방지 프롬프트 3원칙**:
    1. **근거 제한**: "제시된 참고 자료에 있는 내용만 바탕으로 답변할 것."
    2. **탈출구 제공**: "참고 자료에서 답을 찾을 수 없다면 지어내지 말고 모른다고 답변할 것."
    3. **출처 표기 요구**: "답변 끝에 참고한 페이지/출처를 명시할 것."
  * **Threshold 방어선**: 검색 결과의 유사도가 임계점(예: 0.35) 미만이면 LLM 호출을 차단하여 비용을 절약하고 환각을 원천 차단합니다.

* **Pure Python 구현 vs LangChain LCEL 리팩토링 비교**

| 역할 | Pure Python (`02_rag_pure.py`) | LangChain LCEL (`03_rag_langchain.py`) |
| :--- | :--- | :--- |
| **① 검색 (Retrieval)** | `retrieve()` (Chroma 직접 조회) | `vectorstore.as_retriever(search_kwargs={"k": 3})` |
| **② 증강 (Augment)** | `build_prompt()` (f-string 조합) | `ChatPromptTemplate.from_messages()` |
| **③ 생성 (Generation)** | `client.models.generate_content()` | `ChatGoogleGenerativeAI(model="gemini-2.5-flash")` |
| **④ 파이프라인 조립** | 순차적 함수 호출 | **`chain = RunnablePassthrough() \| prompt \| llm \| parser`** |
| **⑤ 출력 정제** | `response.text` 직접 추출 | `StrOutputParser()` |

* **LCEL (LangChain Expression Language)**:
  * 파이프라인 연산자 `|`를 사용하여 검색, 프롬프트 생성, 모델 호출, 응답 파싱을 하드코딩 없이 표준 인터페이스로 결합합니다.
  * 모델이나 저장소가 변경되어도 핵심 생성 체인의 변경 없이 유연하게 교체할 수 있는 확장성을 확보합니다.

---

## 🛠️ Function Calling 및 Spring Boot - FastAPI 통합 아키텍처

### 1. Function Calling (Tool Calling) 메커니즘

* **RAG와 Function Calling의 역할 분담**
  * **RAG**: 사내 규정 PDF, 강좌 설명서 등 미리 임베딩되어 정적 Vector DB에 저장 가능한 데이터를 조회할 때 사용합니다.
  * **Function Calling**: "내 현재 진도율", "마감 임박 과제" 등 특정 사용자별로 실시간 변경되는 RDB(MySQL) 데이터를 AI가 조회해야 할 때 사용합니다.

* **동작 원리**:
  1. 사용자가 질문을 입력합니다.
  2. Gemini LLM이 질문을 분석하여 미리 등록된 도구(함수) 중 실행이 필요한 **함수 이름과 파라미터**를 결정하여 요청합니다.
  3. FastAPI 서버가 해당 함수(Spring API 역호출 등)를 실제로 실행하고 정형 데이터를 획득합니다.
  4. 획득한 실행 결과를 다시 LLM에게 전달하여 최종 자연어 답변을 완성합니다.

---

### 2. Spring Boot ↔ FastAPI 연동 아키텍처 및 설정 실무

#### 전체 시스템 데이터 흐름도
Client (Browser)
│ (POST /chat)
▼
[Spring Boot Server :8080] ────(RestClient / B 구간)────► [FastAPI AI Server :8000]
(신원 검증: student_id 주입)                                   │
▲                                                           │ (Function Calling / C 구간)
└─────────────(StudentApiController 역호출)─────────────────┘

#### 핵심 구축 및 설정 규칙

* **1. 신원 검증 책임 및 보안 원칙 (Spring ChatController)**
  * 브라우저가 전달하는 `student_id`는 타인의 학번으로 도용될 수 있으므로 절대 신뢰하지 않습니다.
  * 신원 확정은 **반드시 Spring 서버의 책임**이며, 인증 객체(세션/JWT)에서 추출하여 백엔드가 FastAPI 요청 DTO에 `student_id`를 직접 주입합니다.

* **2. CORS 정책의 명확한 경계 (`CorsConfig.java`)**
  * **CORS는 브라우저 내부에서만 동작하는 보안 정책**입니다.
  * (A) **브라우저 → Spring**: CORS 설정 필수 (`allowedOrigins`에 구체적 Origin 명시, `*` 사용 금지).
  * (B) **Spring → FastAPI** & (C) **FastAPI → Spring**: 서버 대 서버 HTTP 통신(RestClient, httpx)이므로 CORS 정책과 완전히 무관합니다.

* **3. HTTP 클라이언트 타임아웃 및 장애 격리 (`RestClientConfig.java` & `AiClientService.java`)**
  * **타임아웃 명시적 설정**:
    * Connect Timeout: 3초 (FastAPI 서버의 생존 여부를 신속히 판단).
    * Read Timeout: **60초** (LLM이 Function Calling을 다단계로 수행하며 답변을 생성하는 특성 고려).
    * 타임아웃 미설정 시 FastAPI 장애 발생 시 Spring의 Tomcat 스레드 풀(기본 200개)이 무기한 대기로 고갈되어 회원가입 등 전체 서비스 장애로 확산됩니다.
  * **장애 격리 (Bulkhead 패턴)**:
    * AI 서버가 다운되거나 타임아웃(`ResourceAccessException`)이 발생하거나 `4xx/5xx` 에러가 반환되어도 예외를 상위로 던지지 않습니다.
    * Spring 계층에서 예외를 가로채 정중한 실패 메시지("현재 AI 도우미 서비스가 원활하지 않습니다.")로 변환하여 반환함으로써 챗봇 기능이 죽어도 LMS 본래 기능(수강신청, 과제 제출 등)은 정상 동작하도록 보호합니다.

* **4. 통신 규약 매핑 (`snake_case` 계약)**
  * Java는 `camelCase`(예: `studentId`), FastAPI(Pydantic)는 `snake_case`(예: `student_id`)를 표준으로 사용합니다.
  * 전역 `ObjectMapper`를 수정하면 전체 REST API 규격에 영향을 주므로, FastAPI 통신 전용 DTO에 **`@JsonNaming(PropertyNamingStrategies.SnakeCaseStrategy.class)`**를 지정하여 이종 언어 간 직렬화/역직렬화 경계를 완벽히 분리합니다.
