# 계산기 HTTP 스트리밍 데모

이 프로젝트는 Spring Boot WebFlux를 사용하여 서버 전송 이벤트(SSE)를 통한 HTTP 스트리밍을 보여줍니다. 두 가지 애플리케이션으로 구성됩니다.

- **계산기 서버**: 계산을 수행하고 SSE를 사용하여 결과를 스트리밍하는 반응형 웹 서비스
- **계산기 클라이언트**: 스트리밍 엔드포인트를 사용하는 클라이언트 애플리케이션

## 사전 요구 사항

- Java 17 이상
- Maven 3.6 이상

## 프로젝트 구조

```
java/
├── calculator-server/     # SSE 엔드포인트가 있는 Spring Boot 서버
│   ├── src/main/java/com/example/calculatorserver/
│   │   ├── CalculatorServerApplication.java
│   │   └── CalculatorController.java
│   └── pom.xml
├── calculator-client/     # Spring Boot 클라이언트 애플리케이션
│   ├── src/main/java/com/example/calculatorclient/
│   │   └── CalculatorClientApplication.java
│   └── pom.xml
└── README.md
```

## 작동 방식

1. **계산기 서버**는 `/calculate` 엔드포인트를 노출합니다.
   - 쿼리 매개변수 `a`(숫자), `b`(숫자), `op`(작업)를 허용합니다.
   - 지원되는 작업: `add`, `sub`, `mul`, `div`
   - 계산 진행률 및 결과를 포함하는 서버 전송 이벤트를 반환합니다.

2. **계산기 클라이언트**는 서버에 연결하고 다음을 수행합니다.
   - `7 * 5`를 계산하는 요청을 합니다.
   - 스트리밍 응답을 사용합니다.
   - 각 이벤트를 콘솔에 출력합니다.

## 애플리케이션 실행

### 옵션 1: Maven 사용(권장)

#### 1. 계산기 서버 시작

터미널을 열고 서버 디렉토리로 이동합니다.

```bash
cd calculator-server
mvn clean package
mvn spring-boot:run
```

서버는 `http://localhost:8080`에서 시작됩니다.

다음과 같은 출력이 표시되어야 합니다.
```
CalculatorServerApplication이 X.XXX초 만에 시작되었습니다.
Netty가 포트 8080(http)에서 시작되었습니다.
```

#### 2. 계산기 클라이언트 실행

**새 터미널**을 열고 클라이언트 디렉토리로 이동합니다.

```bash
cd calculator-client
mvn clean package
mvn spring-boot:run
```

클라이언트는 서버에 연결하고 계산을 수행하며 스트리밍 결과를 표시합니다.

### 옵션 2: Java 직접 사용

#### 1. 서버 컴파일 및 실행:

```bash
cd calculator-server
mvn clean package
java -jar target/calculator-server-0.0.1-SNAPSHOT.jar
```

#### 2. 클라이언트 컴파일 및 실행:

```bash
cd calculator-client
mvn clean package
java -jar target/calculator-client-0.0.1-SNAPSHOT.jar
```

## 서버 수동 테스트

웹 브라우저 또는 curl을 사용하여 서버를 테스트할 수도 있습니다.

### 웹 브라우저 사용:
방문: `http://localhost:8080/calculate?a=10&b=5&op=add`

### curl 사용:
```bash
curl "http://localhost:8080/calculate?a=10&b=5&op=add" -H "Accept: text/event-stream"
```

## 예상 출력

클라이언트를 실행하면 다음과 유사한 스트리밍 출력이 표시되어야 합니다.

```
event:info
data:계산 중: 7.0 mul 5.0

event:result
data:35.0
```

## 지원되는 작업

- `add` - 덧셈 (a + b)
- `sub` - 뺄셈 (a - b)
- `mul` - 곱셈 (a * b)
- `div` - 나눗셈 (a / b, b = 0이면 NaN 반환)

## API 참조

### GET /calculate

**매개변수:**
- `a`(필수): 첫 번째 숫자(double)
- `b`(필수): 두 번째 숫자(double)
- `op`(필수): 작업(`add`, `sub`, `mul`, `div`)

**응답:**
- Content-Type: `text/event-stream`
- 계산 진행률 및 결과를 포함하는 서버 전송 이벤트를 반환합니다.

**요청 예시:**
```
GET /calculate?a=7&b=5&op=mul HTTP/1.1
Host: localhost:8080
Accept: text/event-stream
```

**응답 예시:**
```
event: info
data: 계산 중: 7.0 mul 5.0

event: result
data: 35.0
```

## 문제 해결

### 일반적인 문제

1. **포트 8080이 이미 사용 중임**
   - 포트 8080을 사용하는 다른 애플리케이션을 중지합니다.
   - 또는 `calculator-server/src/main/resources/application.yml`에서 서버 포트를 변경합니다.

2. **연결 거부됨**
   - 클라이언트를 시작하기 전에 서버가 실행 중인지 확인합니다.
   - 서버가 포트 8080에서 성공적으로 시작되었는지 확인합니다.

3. **매개변수 이름 문제**
   - 이 프로젝트에는 `-parameters` 플래그가 있는 Maven 컴파일러 구성이 포함되어 있습니다.
   - 매개변수 바인딩 문제가 발생하면 프로젝트가 이 구성으로 빌드되었는지 확인하십시오.

### 애플리케이션 중지

- 각 애플리케이션이 실행 중인 터미널에서 `Ctrl+C`를 누릅니다.
- 또는 백그라운드 프로세스로 실행 중인 경우 `mvn spring-boot:stop`을 사용합니다.

## 기술 스택

- **Spring Boot 3.3.1** - 애플리케이션 프레임워크
- **Spring WebFlux** - 반응형 웹 프레임워크
- **Project Reactor** - 반응형 스트림 라이브러리
- **Netty** - 비차단 I/O 서버
- **Maven** - 빌드 도구
- **Java 17+** - 프로그래밍 언어

## 다음 단계

코드를 수정하여 다음을 시도해 보십시오.
- 더 많은 수학 연산 추가
- 잘못된 연산에 대한 오류 처리 포함
- 요청/응답 로깅 추가
- 인증 구현
- 단위 테스트 추가