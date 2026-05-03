# 📔 모듈 1: 전체 학습 기록 (2.24 ~ 3.25)

## 🛠️ 컴퓨터 기초 및 형상 관리

### 1. 운영체제와 인터페이스
* **인터페이스의 진화:** 천공카드(0, 1) → 타자기(CLI) → 마우스(GUI)
* **OS 구조:** 애플리케이션 > 쉘(Shell) > 운영체제 > 커널 > 하드웨어
* **CLI (Command Line Interface):**
  * 쉘은 사용자와 커널을 연결하는 매개체(조개껍데기)
  * **개발자에게 중요한 이유:** 자동화(CI/CD), 재현 가능성, 세밀한 제어, 스크립팅 가능
  * **주요 도구:** Git, Docker, AWS, Linux 서버 관리

### 2. Git 형상관리 (Version Control)
* **목적:** 코드 변경 사항 추적, 버전 관리(되돌리기), 협업 및 충돌 방지
* **구조 및 흐름:**
  * **Working Directory:** 실제 파일 수정 공간
  * **Staging Area (add):** 커밋 대기소 (인덱스라고도 함)
  * **Local Repository (commit):** 내 컴퓨터의 최종 저장
  * **Remote Repository (push):** GitHub 등의 원격 서버 공유
* **주요 개념:**
  * `init`: 로컬 저장소 초기화
  * `status`: 파일 상태 확인 (untracked, modified, staged)
  * `.gitignore`: 보안(비밀번호) 및 불필요한 파일 제외 설정
  * `Branch`: 독립적인 작업 공간 분리
  * `Merge`: 작업 내용 합치기 (충돌 발생 시 수동 해결 필수)

---

## ☕ Java 프로그래밍 기초

### 1. Java 언어의 특징 및 실행 원리
* **특징:** 객체지향, 플랫폼 독립적(JVM), 안정성 중심
* **실행 순서:** `.java 소스` → `javac 컴파일` → `.class 바이트코드` → `JVM 해석` → `OS 실행`
* **JVM 메모리 구조:**
  * **Method Area:** 클래스 정보, static 변수
  * **Stack Area:** 메서드 호출, 지역 변수 (LIFO)
  * **Heap Area:** 실제 객체(Instance) 저장소 (가비지 컬렉터가 관리)

### 2. 기본 문법 및 연산
* **변수와 자료형:** 리터럴(값)과 변수(공간). 기본형과 참조형의 차이
* **형변환:** 자동 형변환(작은→큰)과 강제 형변환(큰→작은, 데이터 손실 위험)
* **연산자:** 산술, 비교, 논리 연산. `&&`와 `||`의 단락 평가(Short-circuit) 특성
* **조건문/반복문:**
  * `if-else`, `switch-case`
  * `for`(횟수 중심), `while`(조건 중심), `do-while`(최소 1회 보장)
* **배열:** 동일 타입 묶음. 힙 영역에 생성. 인덱스는 0부터 시작

---

## 🏗️ Java 객체지향 및 고급

### 1. 클래스와 객체 (OOP)
* **구성:** 필드(상태), 메서드(행위), 생성자(초기화)
* **캡슐화:** 접근 제어자(`private`, `public` 등)를 통한 정보 은닉
* **상속 및 다형성:** 부모 클래스 재사용 및 인터페이스를 통한 유연한 설계

### 2. 예외 처리 (Exception)
* **try-catch-finally:** 에러 발생 시 프로그램 중단 방지
* **try-with-resources:** 자원(DB 연결 등) 자동 해제 기능

---

## 🗄️ 데이터베이스 및 JDBC 고도화

### 1. MySQL과 SQL 실습
* **DML/DDL:** 데이터 조작(Insert, Select, Update, Delete) 및 정의
* **관계 설계:** 기본키(PK), 외래키(FK)를 통한 테이블 간 연관 관계 구축

### 2. JDBC (Java Database Connectivity) 기초
* **표준 인터페이스:** DB 종류에 상관없이 자바에서 접속 가능하게 하는 규격
* **실행 절차:**
  1. 드라이버 로드
  2. Connection 객체 생성 (DriverManager)
  3. Statement 또는 PreparedStatement 실행
  4. ResultSet 결과 처리
  5. 자원 해제 (close)

### 3. 커넥션 풀 (Connection Pool) - HikariCP
* **도입 이유:** 매번 DB를 연결/해제하는 작업은 비용(시간/자원)이 매우 큼
* **동작:** 커넥션을 미리 만들어 Pool에 보관 후 대여/반납 방식
* **상세 설정 (static 블록 활용):**
  * `maximumPoolSize`: 최대 커넥션 개수 (기본 10)
  * `minimumIdle`: 최소 대기 커넥션
  * `connectionTimeout`: 연결 대기 제한 시간 (2000ms)
  * `maxLifetime`: 커넥션 교체 주기 (30분)
  * **ClassLoader:** 클래스 경로에서 `db-info.properties` 파일을 스트림으로 로드하여 설정 자동화

### 4. 쿼리 최적화 및 유틸리티 (QueryUtil)
* **문제:** 자바 코드 내 SQL 하드코딩은 유지보수를 어렵게 함
* **해결:** `queries.xml` 파일에 SQL을 분리 저장
* **구현 로직:**
  1. `DocumentBuilderFactory`로 XML 파서 생성
  2. XML 문서 로드 및 `entry` 태그 분석
  3. `key` 속성을 쿼리 ID로, 내부 텍스트를 SQL 문으로 맵핑하여 `Properties`에 저장
  4. 자바 로직에서는 ID만 호출하여 쿼리 사용

---

## 💡 실무 팁 및 기타 필기 요약
* **주석의 중요성:** 왜 이 코드를 짰는지, 어떤 예외를 고려했는지 기록
* **보안:** DB 접속 정보는 반드시 별도 파일로 관리하고 Git 추적 제외
* **메모리 효율:** 불필요한 객체 생성을 줄이고 static 활용 타이밍 숙지
