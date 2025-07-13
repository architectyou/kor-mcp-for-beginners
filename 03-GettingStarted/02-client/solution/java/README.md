# MCP Java 클라이언트 - 계산기 데모

이 프로젝트는 MCP(모델 컨텍스트 프로토콜) 서버에 연결하고 상호 작용하는 Java 클라이언트를 만드는 방법을 보여줍니다. 이 예에서는 1장의 계산기 서버에 연결하여 다양한 수학 연산을 수행합니다.

## 사전 요구 사항

이 클라이언트를 실행하기 전에 다음을 수행해야 합니다.

1. **1장의 계산기 서버 시작**:
   - 계산기 서버 디렉토리로 이동합니다. `03-GettingStarted/01-first-server/solution/java/`
   - 계산기 서버를 빌드하고 실행합니다.
     ```cmd
     cd ..\01-first-server\solution\java
     .\mvnw clean install -DskipTests
     java -jar target\calculator-server-0.0.1-SNAPSHOT.jar
     ```
   - 서버는 `http://localhost:8080`에서 실행되어야 합니다.

2. **시스템에 Java 21 이상 설치**
3. **Maven**(Maven 래퍼를 통해 포함됨)

## SDKClient란 무엇입니까?

`SDKClient`는 다음을 보여주는 Java 애플리케이션입니다.
- 서버 전송 이벤트(SSE) 전송을 사용하여 MCP 서버에 연결
- 서버에서 사용 가능한 도구 나열
- 다양한 계산기 기능을 원격으로 호출
- 응답 처리 및 결과 표시

## 작동 방식

클라이언트는 Spring AI MCP 프레임워크를 사용하여 다음을 수행합니다.

1. **연결 설정**: 계산기 서버에 연결하기 위해 WebFlux SSE 클라이언트 전송을 만듭니다.
2. **클라이언트 초기화**: MCP 클라이언트를 설정하고 연결을 설정합니다.
3. **도구 검색**: 사용 가능한 모든 계산기 작업을 나열합니다.
4. **작업 실행**: 샘플 데이터로 다양한 수학 함수를 호출합니다.
5. **결과 표시**: 각 계산 결과를 표시합니다.

## 프로젝트 구조

```
src/
└── main/
    └── java/
        └── com/
            └── microsoft/
                └── mcp/
                    └── sample/
                        └── client/
                            └── SDKClient.java    # 주 클라이언트 구현
```

## 주요 종속성

이 프로젝트는 다음과 같은 주요 종속성을 사용합니다.

```xml
<dependency>
    <groupId>org.springframework.ai</groupId>
    <artifactId>spring-ai-starter-mcp-server-webflux</artifactId>
</dependency>
```

이 종속성은 다음을 제공합니다.
- `McpClient` - 주 클라이언트 인터페이스
- `WebFluxSseClientTransport` - 웹 기반 통신을 위한 SSE 전송
- MCP 프로토콜 스키마 및 요청/응답 유형

## 프로젝트 빌드

Maven 래퍼를 사용하여 프로젝트를 빌드합니다.

```cmd
.\mvnw clean install
```

## 클라이언트 실행

```cmd
java -jar .\target\calculator-client-0.0.1-SNAPSHOT.jar
```

**참고**: 이러한 명령을 실행하기 전에 계산기 서버가 `http://localhost:8080`에서 실행 중인지 확인하십시오.

## 클라이언트가 하는 일

클라이언트를 실행하면 다음을 수행합니다.

1. **연결**: `http://localhost:8080`의 계산기 서버에 연결합니다.
2. **도구 나열** - 사용 가능한 모든 계산기 작업을 표시합니다.
3. **계산 수행**:
   - 덧셈: 5 + 3 = 8
   - 뺄셈: 10 - 4 = 6
   - 곱셈: 6 × 7 = 42
   - 나눗셈: 20 ÷ 4 = 5
   - 거듭제곱: 2^8 = 256
   - 제곱근: √16 = 4
   - 절대값: |-5.5| = 5.5
   - 도움말: 사용 가능한 작업을 표시합니다.

## 예상 출력

```
사용 가능한 도구 = ListToolsResult[tools=[Tool[name=add, description=두 숫자 더하기, ...], ...]]
더하기 결과 = CallToolResult[content=[TextContent[text="5,00 + 3,00 = 8,00"]], isError=false]
빼기 결과 = CallToolResult[content=[TextContent[text="10,00 - 4,00 = 6,00"]], isError=false]
곱하기 결과 = CallToolResult[content=[TextContent[text="6,00 * 7,00 = 42,00"]], isError=false]
나누기 결과 = CallToolResult[content=[TextContent[text="20,00 / 4,00 = 5,00"]], isError=false]
거듭제곱 결과 = CallToolResult[content=[TextContent[text="2,00 ^ 8,00 = 256,00"]], isError=false]
제곱근 결과 = CallToolResult[content=[TextContent[text="√16,00 = 4,00"]], isError=false]
절대값 결과 = CallToolResult[content=[TextContent[text="|-5,50| = 5,50"]], isError=false]
도움말 = CallToolResult[content=[TextContent[text="기본 계산기 MCP 서비스\n\n사용 가능한 작업:\n1. add(a, b) - 두 숫자 더하기\n2. subtract(a, b) - 첫 번째 숫자에서 두 번째 숫자 빼기\n..."]], isError=false]
```

**참고**: 마지막에 남아 있는 스레드에 대한 Maven 경고가 표시될 수 있습니다. 이는 반응형 애플리케이션의 경우 정상이며 오류를 나타내지 않습니다.

## 코드 이해

### 1. 전송 설정
```java
var transport = new WebFluxSseClientTransport(WebClient.builder().baseUrl("http://localhost:8080"));
```
계산기 서버에 연결하는 SSE(Server-Sent Events) 전송을 만듭니다.

### 2. 클라이언트 생성
```java
var client = McpClient.sync(this.transport).build();
client.initialize();
```
동기 MCP 클라이언트를 만들고 연결을 초기화합니다.

### 3. 도구 호출
```java
CallToolResult resultAdd = client.callTool(new CallToolRequest("add", Map.of("a", 5.0, "b", 3.0)));
```
매개변수 a=5.0 및 b=3.0으로 "add" 도구를 호출합니다.

## 문제 해결

### 서버가 실행되지 않음
연결 오류가 발생하면 1장의 계산기 서버가 실행 중인지 확인하십시오.
```
오류: 연결 거부됨
```
**해결책**: 먼저 계산기 서버를 시작하십시오.

### 포트가 이미 사용 중임
포트 8080이 사용 중인 경우:
```
오류: 주소가 이미 사용 중임
```
**해결책**: 포트 8080을 사용하는 다른 애플리케이션을 중지하거나 서버를 다른 포트를 사용하도록 수정하십시오.

### 빌드 오류
빌드 오류가 발생하면 다음을 수행하십시오.
```cmd
.\mvnw clean install -DskipTests
```

## 자세히 알아보기

- [Spring AI MCP 설명서](https://docs.spring.io/spring-ai/reference/api/mcp/)
- [모델 컨텍스트 프로토콜 사양](https://modelcontextprotocol.io/)
- [Spring WebFlux 설명서](https://docs.spring.io/spring-framework/docs/current/reference/html/web-reactive.html)
