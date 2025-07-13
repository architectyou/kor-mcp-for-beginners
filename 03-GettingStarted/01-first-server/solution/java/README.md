# 기본 계산기 MCP 서비스

이 서비스는 Spring Boot와 WebFlux 전송을 사용하여 모델 컨텍스트 프로토콜(MCP)을 통해 기본 계산기 작업을 제공합니다. MCP 구현에 대해 배우는 초보자를 위한 간단한 예제로 설계되었습니다.

자세한 내용은 [MCP 서버 부트 스타터](https://docs.spring.io/spring-ai/reference/api/mcp/mcp-server-boot-starter-docs.html) 참조 설명서를 참조하십시오.


## 서비스 사용

이 서비스는 MCP 프로토콜을 통해 다음 API 엔드포인트를 노출합니다.

- `add(a, b)`: 두 숫자를 더합니다.
- `subtract(a, b)`: 첫 번째 숫자에서 두 번째 숫자를 뺍니다.
- `multiply(a, b)`: 두 숫자를 곱합니다.
- `divide(a, b)`: 첫 번째 숫자를 두 번째 숫자로 나눕니다(0으로 나누기 확인 포함).
- `power(base, exponent)`: 숫자의 거듭제곱을 계산합니다.
- `squareRoot(number)`: 제곱근을 계산합니다(음수 확인 포함).
- `modulus(a, b)`: 나눌 때의 나머지를 계산합니다.
- `absolute(number)`: 절대값을 계산합니다.

## 종속성

이 프로젝트에는 다음과 같은 주요 종속성이 필요합니다.

```xml
<dependency>
    <groupId>org.springframework.ai</groupId>
    <artifactId>spring-ai-starter-mcp-server-webflux</artifactId>
</dependency>
```

## 프로젝트 빌드

Maven을 사용하여 프로젝트를 빌드합니다.
```bash
./mvnw clean install -DskipTests
```

## 서버 실행

### Java 사용

```bash
java -jar target/calculator-server-0.0.1-SNAPSHOT.jar
```

### MCP Inspector 사용

MCP Inspector는 MCP 서비스와 상호 작용하는 데 유용한 도구입니다. 이 계산기 서비스와 함께 사용하려면 다음을 수행하십시오.

1. **새 터미널 창에 MCP Inspector를 설치하고 실행합니다.**
   ```bash
   npx @modelcontextprotocol/inspector
   ```

2. **앱에 표시된 URL(일반적으로 http://localhost:6274)을 클릭하여 웹 UI에 액세스합니다.**

3. **연결 구성:**
   - 전송 유형을 "SSE"로 설정합니다.
   - URL을 실행 중인 서버의 SSE 엔드포인트로 설정합니다. `http://localhost:8080/sse`
   - "연결"을 클릭합니다.

4. **도구 사용:**
   - "도구 목록"을 클릭하여 사용 가능한 계산기 작업을 확인합니다.
   - 도구를 선택하고 "도구 실행"을 클릭하여 작업을 실행합니다.

![MCP Inspector 스크린샷](images/tool.png)
