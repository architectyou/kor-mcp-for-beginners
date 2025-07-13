# MCP 시작하기

모델 컨텍스트 프로토콜(MCP)의 첫걸음을 내딛는 것을 환영합니다! MCP를 처음 접하든 이해를 심화시키고자 하든, 이 가이드는 필수적인 설정 및 개발 과정을 안내합니다. MCP가 AI 모델과 애플리케이션 간의 원활한 통합을 어떻게 가능하게 하는지 알아보고, MCP 기반 솔루션을 구축하고 테스트하기 위해 환경을 신속하게 준비하는 방법을 배웁니다.

> 요약: AI 앱을 구축하는 경우 LLM(대규모 언어 모델)에 도구 및 기타 리소스를 추가하여 LLM을 더 잘 알 수 있다는 것을 알고 있습니다. 그러나 해당 도구와 리소스를 서버에 배치하면 LLM이 있거나 없는 모든 클라이언트에서 앱과 서버 기능을 사용할 수 있습니다.

## 개요

이 단원에서는 MCP 환경을 설정하고 첫 번째 MCP 애플리케이션을 구축하는 실용적인 지침을 제공합니다. 필요한 도구와 프레임워크를 설정하고, 기본 MCP 서버를 구축하고, 호스트 애플리케이션을 만들고, 구현을 테스트하는 방법을 배웁니다.

모델 컨텍스트 프로토콜(MCP)은 애플리케이션이 LLM에 컨텍스트를 제공하는 방법을 표준화하는 개방형 프로토콜입니다. MCP를 AI 애플리케이션용 USB-C 포트라고 생각하십시오. AI 모델을 다양한 데이터 소스 및 도구에 연결하는 표준화된 방법을 제공합니다.

## 학습 목표

이 단원을 마치면 다음을 수행할 수 있습니다.

- C#, Java, Python, TypeScript 및 JavaScript에서 MCP용 개발 환경 설정
- 사용자 지정 기능(리소스, 프롬프트 및 도구)으로 기본 MCP 서버 구축 및 배포
- MCP 서버에 연결하는 호스트 애플리케이션 만들기
- MCP 구현 테스트 및 디버깅

## MCP 환경 설정

MCP 작업을 시작하기 전에 개발 환경을 준비하고 기본 워크플로를 이해하는 것이 중요합니다. 이 섹션에서는 MCP를 원활하게 시작하기 위한 초기 설정 단계를 안내합니다.

### 사전 요구 사항

MCP 개발에 뛰어들기 전에 다음을 확인하십시오.

- **개발 환경**: 선택한 언어(C#, Java, Python, TypeScript 또는 JavaScript)용
- **IDE/편집기**: Visual Studio, Visual Studio Code, IntelliJ, Eclipse, PyCharm 또는 최신 코드 편집기
- **패키지 관리자**: NuGet, Maven/Gradle, pip 또는 npm/yarn
- **API 키**: 호스트 애플리케이션에서 사용할 계획인 모든 AI 서비스용


## 기본 MCP 서버 구조

MCP 서버에는 일반적으로 다음이 포함됩니다.

- **서버 구성**: 포트, 인증 및 기타 설정 구성
- **리소스**: LLM에서 사용할 수 있는 데이터 및 컨텍스트
- **도구**: 모델이 호출할 수 있는 기능
- **프롬프트**: 텍스트 생성 또는 구조화를 위한 템플릿


다음은 TypeScript의 간소화된 예제입니다.

```typescript
import { Server, Tool, Resource } from "@modelcontextprotocol/typescript-server-sdk";

// 새 MCP 서버 만들기
const server = new Server({
  port: 3000,
  name: "Example MCP Server",
  version: "1.0.0"
});

// 도구 등록
server.registerTool({
  name: "calculator",
  description: "기본 계산 수행",
  parameters: {
    expression: {
      type: "string",
      description: "평가할 수학 표현식"
    }
  },
  handler: async (params) => {
    const result = eval(params.expression);
    return { result };
  }
});

// 서버 시작
server.start();
```

앞의 코드에서는 다음을 수행했습니다.

- MCP TypeScript SDK에서 필요한 클래스를 가져옵니다.
- 새 MCP 서버 인스턴스를 만들고 구성합니다.
- 처리기 함수를 사용하여 사용자 지정 도구(`calculator`)를 등록합니다.
- 들어오는 MCP 요청을 수신하도록 서버를 시작합니다.


## 테스트 및 디버깅

MCP 서버 테스트를 시작하기 전에 디버깅에 사용할 수 있는 도구와 모범 사례를 이해하는 것이 중요합니다. 효과적인 테스트는 서버가 예상대로 작동하는지 확인하고 문제를 신속하게 식별하고 해결하는 데 도움이 됩니다. 다음 섹션에서는 MCP 구현을 확인하기 위한 권장 접근 방식을 간략하게 설명합니다.

MCP는 서버를 테스트하고 디버깅하는 데 도움이 되는 도구를 제공합니다.

- **Inspector 도구**, 이 그래픽 인터페이스를 사용하면 서버에 연결하고 도구, 프롬프트 및 리소스를 테스트할 수 있습니다.
- **curl**, curl과 같은 명령줄 도구나 HTTP 명령을 만들고 실행할 수 있는 다른 클라이언트를 사용하여 서버에 연결할 수도 있습니다.

### MCP Inspector 사용

[MCP Inspector](https://github.com/modelcontextprotocol/inspector)는 다음을 수행하는 데 도움이 되는 시각적 테스트 도구입니다.

1. **서버 기능 검색**: 사용 가능한 리소스, 도구 및 프롬프트를 자동으로 검색합니다.
2. **도구 실행 테스트**: 다양한 매개변수를 시도하고 실시간으로 응답을 확인합니다.
3. **서버 메타데이터 보기**: 서버 정보, 스키마 및 구성을 검사합니다.

```bash
# 예: TypeScript, MCP Inspector 설치 및 실행
npx @modelcontextprotocol/inspector node build/index.js
```

위 명령을 실행하면 MCP Inspector가 브라우저에서 로컬 웹 인터페이스를 시작합니다. 등록된 MCP 서버, 사용 가능한 도구, 리소스 및 프롬프트가 표시되는 대시보드가 표시됩니다. 이 인터페이스를 사용하면 도구 실행을 대화식으로 테스트하고, 서버 메타데이터를 검사하고, 실시간 응답을 볼 수 있으므로 MCP 서버 구현을 더 쉽게 확인하고 디버깅할 수 있습니다.

다음은 어떻게 보일 수 있는지에 대한 스크린샷입니다.

![](/03-GettingStarted/01-first-server/assets/connected.png)

## 일반적인 설정 문제 및 해결 방법

| 문제 | 가능한 해결 방법 |
|---|---|
| 연결 거부 | 서버가 실행 중이고 포트가 올바른지 확인하십시오. |
| 도구 실행 오류 | 매개변수 유효성 검사 및 오류 처리를 검토하십시오. |
| 인증 실패 | API 키 및 권한을 확인하십시오. |
| 스키마 유효성 검사 오류 | 매개변수가 정의된 스키마와 일치하는지 확인하십시오. |
| 서버가 시작되지 않음 | 포트 충돌 또는 누락된 종속성을 확인하십시오. |
| CORS 오류 | 교차 출처 요청에 대한 적절한 CORS 헤더를 구성하십시오. |
| 인증 문제 | 토큰 유효성 및 권한을 확인하십시오. |

## 로컬 개발

로컬 개발 및 테스트를 위해 컴퓨터에서 직접 MCP 서버를 실행할 수 있습니다.

1. **서버 프로세스 시작**: MCP 서버 애플리케이션을 실행합니다.
2. **네트워킹 구성**: 서버가 예상 포트에서 액세스할 수 있는지 확인합니다.
3. **클라이언트 연결**: `http://localhost:3000`과 같은 로컬 연결 URL을 사용합니다.

```bash
# 예: TypeScript MCP 서버를 로컬에서 실행
npm run start
# 서버가 http://localhost:3000에서 실행 중입니다.
```

## 첫 번째 MCP 서버 구축

이전 단원에서 [핵심 개념](/01-CoreConcepts/README.md)을 다루었으므로 이제 해당 지식을 실제로 적용할 차례입니다.

### 서버가 할 수 있는 일

코드를 작성하기 전에 서버가 할 수 있는 일을 상기시켜 보겠습니다.

MCP 서버는 예를 들어 다음을 수행할 수 있습니다.

- 로컬 파일 및 데이터베이스에 액세스
- 원격 API에 연결
- 계산 수행
- 다른 도구 및 서비스와 통합
- 상호 작용을 위한 사용자 인터페이스 제공

좋습니다. 이제 우리가 할 수 있는 일을 알았으니 코딩을 시작하겠습니다.

## 연습: 서버 만들기

서버를 만들려면 다음 단계를 따라야 합니다.

- MCP SDK를 설치합니다.
- 프로젝트를 만들고 프로젝트 구조를 설정합니다.
- 서버 코드를 작성합니다.
- 서버를 테스트합니다.

### -1- SDK 설치

선택한 런타임에 따라 약간 다르므로 아래 런타임 중 하나를 선택하십시오.

생성 AI는 텍스트, 이미지, 심지어 코드까지 생성할 수 있습니다.

<details>
  <summary>TypeScript</summary>

  ```sh
  npm install @modelcontextprotocol/sdk zod
  npm install -D @types/node typescript
  ```

</details>

<details>
<summary>Python</summary>

```sh
# 서버 개발용
pip install "mcp[cli]"
```

</details>

<details>
<summary>.NET</summary>

```sh
dotnet new console -n McpCalculatorServer
cd McpCalculatorServer
```

</details>

<details>
<summary>Java</summary>

Java의 경우 Spring Boot 프로젝트를 만듭니다.

```bash
curl https://start.spring.io/starter.zip \
  -d dependencies=web \
  -d javaVersion=21 \
  -d type=maven-project \
  -d groupId=com.example \
  -d artifactId=calculator-server \
  -d name=McpServer \
  -d packageName=com.microsoft.mcp.sample.server \
  -o calculator-server.zip
```

zip 파일 압축 풀기:

```bash
unzip calculator-server.zip -d calculator-server
cd calculator-server
# 선택 사항: 사용하지 않는 테스트 제거
rm -rf src/test/java
```

*pom.xml* 파일에 다음 전체 구성을 추가합니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    
    <!-- 종속성 관리를 위한 Spring Boot 부모 -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.5.0</version>
        <relativePath />
    </parent>

    <!-- 프로젝트 좌표 -->
    <groupId>com.example</groupId>
    <artifactId>calculator-server</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>Calculator Server</name>
    <description>초보자를 위한 기본 계산기 MCP 서비스</description>

    <!-- 속성 -->
    <properties>
        <java.version>21</java.version>
        <maven.compiler.source>21</maven.compiler.source>
        <maven.compiler.target>21</maven.compiler.target>
    </properties>

    <!-- 버전 관리를 위한 Spring AI BOM -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.ai</groupId>
                <artifactId>spring-ai-bom</artifactId>
                <version>1.0.0-SNAPSHOT</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <!-- 종속성 -->
    <dependencies>
        <dependency>
            <groupId>org.springframework.ai</groupId>
            <artifactId>spring-ai-starter-mcp-server-webflux</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
         <groupId>org.springframework.boot</groupId>
         <artifactId>spring-boot-starter-test</artifactId>
         <scope>test</scope>
      </dependency>
    </dependencies>

    <!-- 빌드 구성 -->
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <release>21</release>
                </configuration>
            </plugin>
        </plugins>
    </build>

    <!-- Spring AI 스냅샷용 저장소 -->
    <repositories>
        <repository>
            <id>spring-milestones</id>
            <name>Spring Milestones</name>
            <url>https://repo.spring.io/milestone</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>
        <repository>
            <id>spring-snapshots</id>
            <name>Spring Snapshots</name>
            <url>https://repo.spring.io/snapshot</url>
            <releases>
                <enabled>false</enabled>
            </releases>
        </repository>
    </repositories>
</project>
```

</details>

### -2- 프로젝트 만들기

이제 SDK가 설치되었으므로 다음에 프로젝트를 만듭니다.

<details>
  <summary>TypeScript</summary>

  ```sh
  mkdir src
  npm install -y
  ```

</details>

<details>
  <summary>Python</summary>

  ```sh
  python -m venv venv
  venv\Scripts\activate
  ```

</details>

<details>
<summary>Java</summary>

```bash
cd calculator-server
./mvnw clean install -DskipTests
```

</details>

### -3- 프로젝트 파일 만들기

<details>
  <summary>TypeScript</summary>
  
  다음 내용으로 *package.json*을 만듭니다.
  
  ```json
  {
     "type": "module",
     "bin": {
       "weather": "./build/index.js"
     },
     "scripts": {
       "build": "tsc && node build/index.js"
     },
     "files": [
       "build"
     ]
  }
  ```

  다음 내용으로 *tsconfig.json*을 만듭니다.

  ```json
  {
    "compilerOptions": {
      "target": "ES2022",
      "module": "Node16",
      "moduleResolution": "Node16",
      "outDir": "./build",
      "rootDir": "./src",
      "strict": true,
      "esModuleInterop": true,
      "skipLibCheck": true,
      "forceConsistentCasingInFileNames": true
    },
    "include": ["src/**/*"],
    "exclude": ["node_modules"]
  }
  ```

</details>

<details>
<summary>Python</summary>

*server.py* 파일 만들기
</details>

<details>
<summary>.NET</summary>

필요한 NuGet 패키지 설치:

```sh
dotnet add package ModelContextProtocol --prerelease
dotnet add package Microsoft.Extensions.Hosting
```

</details>

<details>
<summary>Java</summary>

Java Spring Boot 프로젝트의 경우 프로젝트 구조가 자동으로 생성됩니다.

</details>

### -4- 서버 코드 만들기

<details>
  <summary>TypeScript</summary>
  
  *index.ts* 파일을 만들고 다음 코드를 추가합니다.

  ```typescript
  import { McpServer, ResourceTemplate } from "@modelcontextprotocol/sdk/server/mcp.js";
  import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
  import { z } from "zod";
  
  // MCP 서버 만들기
  const server = new McpServer({
    name: "Demo",
    version: "1.0.0"
  });
  ```

 이제 서버가 있지만 별로 하는 일이 없습니다. 수정해 보겠습니다.
</details>

<details>
<summary>Python</summary>

```python
# server.py
from mcp.server.fastmcp import FastMCP

# MCP 서버 만들기
mcp = FastMCP("Demo")
```

</details>

<details>
<summary>.NET</summary>

```csharp
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;
using ModelContextProtocol.Server;
using System.ComponentModel;

var builder = Host.CreateApplicationBuilder(args);
builder.Logging.AddConsole(consoleLogOptions =>
{
    // 모든 로그를 stderr로 보내도록 구성
    consoleLogOptions.LogToStandardErrorThreshold = LogLevel.Trace;
});

builder.Services
    .AddMcpServer()
    .WithStdioServerTransport()
    .WithToolsFromAssembly();
await builder.Build().RunAsync();

// 기능 추가
```

</details>

<details>
<summary>Java</summary>

Java의 경우 핵심 서버 구성 요소를 만듭니다. 먼저 기본 애플리케이션 클래스를 수정합니다.

*src/main/java/com/microsoft/mcp/sample/server/McpServerApplication.java*:

```java
package com.microsoft.mcp.sample.server;

import org.springframework.ai.tool.ToolCallbackProvider;
import org.springframework.ai.tool.method.MethodToolCallbackProvider;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
import com.microsoft.mcp.sample.server.service.CalculatorService;

@SpringBootApplication
public class McpServerApplication {

    public static void main(String[] args) {
        SpringApplication.run(McpServerApplication.class, args);
    }
    
    @Bean
    public ToolCallbackProvider calculatorTools(CalculatorService calculator) {
        return MethodToolCallbackProvider.builder().toolObjects(calculator).build();
    }
}
```

계산기 서비스 만들기 *src/main/java/com/microsoft/mcp/sample/server/service/CalculatorService.java*:

```java
package com.microsoft.mcp.sample.server.service;

import org.springframework.ai.tool.annotation.Tool;
import org.springframework.stereotype.Service;

/**
 * 기본 계산기 작업을 위한 서비스입니다.
 * 이 서비스는 MCP를 통해 간단한 계산기 기능을 제공합니다.
 */
@Service
public class CalculatorService {

    /**
     * 두 숫자 더하기
     * @param a 첫 번째 숫자
     * @param b 두 번째 숫자
     * @return 두 숫자의 합
     */
    @Tool(description = "두 숫자를 더합니다")
    public String add(double a, double b) {
        double result = a + b;
        return formatResult(a, "+", b, result);
    }

    /**
     * 한 숫자에서 다른 숫자 빼기
     * @param a 뺄 숫자
     * @param b 뺄 숫자
     * @return 뺄셈 결과
     */
    @Tool(description = "첫 번째 숫자에서 두 번째 숫자를 뺍니다")
    public String subtract(double a, double b) {
        double result = a - b;
        return formatResult(a, "-", b, result);
    }

    /**
     * 두 숫자 곱하기
     * @param a 첫 번째 숫자
     * @param b 두 번째 숫자
     * @return 두 숫자의 곱
     */
    @Tool(description = "두 숫자를 곱합니다")
    public String multiply(double a, double b) {
        double result = a * b;
        return formatResult(a, "*", b, result);
    }

    /**
     * 한 숫자를 다른 숫자로 나누기
     * @param a 분자
     * @param b 분모
     * @return 나눗셈 결과
     */
    @Tool(description = "첫 번째 숫자를 두 번째 숫자로 나눕니다")
    public String divide(double a, double b) {
        if (b == 0) {
            return "오류: 0으로 나눌 수 없습니다";
        }
        double result = a / b;
        return formatResult(a, "/", b, result);
    }

    /**
     * 숫자의 거듭제곱 계산
     * @param base 밑
     * @param exponent 지수
     * @return 밑을 지수로 거듭제곱한 결과
     */
    @Tool(description = "숫자의 거듭제곱 계산(밑을 지수로 거듭제곱)")
    public String power(double base, double exponent) {
        double result = Math.pow(base, exponent);
        return formatResult(base, "^", exponent, result);
    }

    /**
     * 숫자의 제곱근 계산
     * @param number 제곱근을 찾을 숫자
     * @return 숫자의 제곱근
     */
    @Tool(description = "숫자의 제곱근 계산")
    public String squareRoot(double number) {
        if (number < 0) {
            return "오류: 음수의 제곱근을 계산할 수 없습니다";
        }
        double result = Math.sqrt(number);
        return String.format("√%.2f = %.2f", number, result);
    }

    /**
     * 나눗셈의 나머지(계수) 계산
     * @param a 피제수
     * @param b 제수
     * @return 나눗셈의 나머지
     */
    @Tool(description = "한 숫자를 다른 숫자로 나눌 때의 나머지 계산")
    public String modulus(double a, double b) {
        if (b == 0) {
            return "오류: 0으로 나눌 수 없습니다";
        }
        double result = a % b;
        return formatResult(a, "%", b, result);
    }

    /**
     * 숫자의 절대값 계산
     * @param number 절대값을 찾을 숫자
     * @return 숫자의 절대값
     */
    @Tool(description = "숫자의 절대값 계산")
    public String absolute(double number) {
        double result = Math.abs(number);
        return String.format("|%.2f| = %.2f", number, result);
    }

    /**
     * 사용 가능한 계산기 작업에 대한 도움말 보기
     * @return 사용 가능한 작업에 대한 정보
     */
    @Tool(description = "사용 가능한 계산기 작업에 대한 도움말 보기")
    public String help() {
        return "기본 계산기 MCP 서비스\n\n" +
               "사용 가능한 작업:\n" +
               "1. add(a, b) - 두 숫자 더하기\n" +
               "2. subtract(a, b) - 첫 번째 숫자에서 두 번째 숫자 빼기\n" +
               "3. multiply(a, b) - 두 숫자 곱하기\n" +
               "4. divide(a, b) - 첫 번째 숫자를 두 번째 숫자로 나누기\n" +
               "5. power(base, exponent) - 숫자를 거듭제곱하기\n" +
               "6. squareRoot(number) - 제곱근 계산\n" + 
               "7. modulus(a, b) - 나눗셈의 나머지 계산\n" +
               "8. absolute(number) - 절대값 계산\n\n" +
               "사용 예: add(5, 3)은 5 + 3 = 8을 반환합니다";
    }

    /**
     * 계산 결과 형식 지정
     */
    private String formatResult(double a, String operator, double b, double result) {
        return String.format("%.2f %s %.2f = %.2f", a, operator, b, result);
    }
}
```

**프로덕션 준비 서비스를 위한 선택적 구성 요소:**

시작 구성 만들기 *src/main/java/com/microsoft/mcp/sample/server/config/StartupConfig.java*:

```java
package com.microsoft.mcp.sample.server.config;

import org.springframework.boot.CommandLineRunner;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class StartupConfig {
    
    @Bean
    public CommandLineRunner startupInfo() {
        return args -> {
            System.out.println("\n" + "=".repeat(60));
            System.out.println("Calculator MCP Server is starting...");
            System.out.println("SSE endpoint: http://localhost:8080/sse");
            System.out.println("Health check: http://localhost:8080/actuator/health");
            System.out.println("=".repeat(60) + "\n");
        };
    }
}
```

상태 컨트롤러 만들기 *src/main/java/com/microsoft/mcp/sample/server/controller/HealthController.java*:

```java
package com.microsoft.mcp.sample.server.controller;

import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import java.time.LocalDateTime;
import java.util.HashMap;
import java.util.Map;

@RestController
public class HealthController {
    
    @GetMapping("/health")
    public ResponseEntity<Map<String, Object>> healthCheck() {
        Map<String, Object> response = new HashMap<>();
        response.put("status", "UP");
        response.put("timestamp", LocalDateTime.now().toString());
        response.put("service", "Calculator MCP Server");
        return ResponseEntity.ok(response);
    }
}
```

예외 처리기 만들기 *src/main/java/com/microsoft/mcp/sample/server/exception/GlobalExceptionHandler.java*:

```java
package com.microsoft.mcp.sample.server.exception;

import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(IllegalArgumentException.class)
    public ResponseEntity<ErrorResponse> handleIllegalArgumentException(IllegalArgumentException ex) {
        ErrorResponse error = new ErrorResponse(
            "Invalid_Input", 
            "잘못된 입력 매개변수: " + ex.getMessage());
        return new ResponseEntity<>(error, HttpStatus.BAD_REQUEST);
    }

    public static class ErrorResponse {
        private String code;
        private String message;

        public ErrorResponse(String code, String message) {
            this.code = code;
            this.message = message;
        }

        // Getters
        public String getCode() { return code; }
        public String getMessage() { return message; }
    }
}
```

사용자 지정 배너 만들기 *src/main/resources/banner.txt*:

```text
_____      _            _       _            
 / ____|    | |          | |     | |           
| |     __ _| | ___ _   _| | __ _| |_ ___  _ __ 
| |    / _` | |/ __| | | | |/ _` | __/ _ \| '__|
| |___| (_| | | (__| |_| | | (_| | || (_) | |   
 \_____\__,_|_|\___|\__,_|_|\__,_|\__\___/|_|   
                                               
Calculator MCP Server v1.0
Spring Boot MCP Application
```

</details>

### -5- 도구 및 리소스 추가

다음 코드를 추가하여 도구 및 리소스를 추가합니다.

<details>
  <summary>TypeScript</summary>

  ```typescript
    server.tool("add",
    { a: z.number(), b: z.number() },
    async ({ a, b }) => ({
      content: [{ type: "text", text: String(a + b) }]
    })
  );

  server.resource(
    "greeting",
    new ResourceTemplate("greeting://{name}", { list: undefined }),
    async (uri, { name }) => ({
      contents: [{
        uri: uri.href,
        text: `안녕하세요, ${name}님!`
      }]
    })
  );
  ```

  도구는 매개변수 `a`와 `b`를 사용하고 다음 형식으로 응답을 생성하는 함수를 실행합니다.

  ```typescript
  {
    contents: [{
      type: "text", content: "일부 콘텐츠"
    }]
  }
  ```

  리소스는 문자열 "greeting"을 통해 액세스되며 매개변수 `name`을 사용하고 도구와 유사한 응답을 생성합니다.

  ```typescript
  {
    uri: "<href>",
    text: "텍스트"
  }
  ```

</details>

<details>
<summary>Python</summary>

```python
# 추가 도구 추가
@mcp.tool()
def add(a: int, b: int) -> int:
    """두 숫자 더하기"""
    return a + b


# 동적 인사말 리소스 추가
@mcp.resource("greeting://{name}")
def get_greeting(name: str) -> str:
    """개인화된 인사말 받기"""
    return f"안녕하세요, {name}님!"
```

앞의 코드에서는 다음을 수행했습니다.

- 두 정수 매개변수 `a`와 `p`를 사용하는 도구 `add`를 정의했습니다.
- 매개변수 `name`을 사용하는 `greeting`이라는 리소스를 만들었습니다.

</details>

<details>
<summary>.NET</summary>

Program.cs 파일에 다음을 추가합니다.

```csharp
[McpServerToolType]
public static class CalculatorTool
{
    [McpServerTool, Description("두 숫자를 더합니다")]
    public static string Add(int a, int b) => $"합계 {a + b}";
}
```

</details>

<details>
<summary>Java</summary>

도구는 이전 단계에서 이미 만들어졌습니다.

</details>

### -6 최종 코드

서버가 시작될 수 있도록 마지막 코드를 추가해 보겠습니다.

<details>
<summary>TypeScript</summary>

```typescript
// stdin에서 메시지 수신 시작 및 stdout에서 메시지 전송 시작
const transport = new StdioServerTransport();
await server.connect(transport);
```

전체 코드는 다음과 같습니다.

```typescript
// index.ts
import { McpServer, ResourceTemplate } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import { z } from "zod";

// MCP 서버 만들기
const server = new McpServer({
  name: "Demo",
  version: "1.0.0"
});

// 추가 도구 추가
server.tool("add",
  { a: z.number(), b: z.number() },
  async ({ a, b }) => ({
    content: [{ type: "text", text: String(a + b) }]
  })
);

// 동적 인사말 리소스 추가
server.resource(
  "greeting",
  new ResourceTemplate("greeting://{name}", { list: undefined }),
  async (uri, { name }) => ({
    contents: [{
      uri: uri.href,
      text: `안녕하세요, ${name}님!`
    }]
  })
);

// stdin에서 메시지 수신 시작 및 stdout에서 메시지 전송 시작
const transport = new StdioServerTransport();
await server.connect(transport);
```

</details>

<details>
<summary>Python</summary>

```python
# server.py
from mcp.server.fastmcp import FastMCP

# MCP 서버 만들기
mcp = FastMCP("Demo")


# 추가 도구 추가
@mcp.tool()
def add(a: int, b: int) -> int:
    """두 숫자 더하기"""
    return a + b


# 동적 인사말 리소스 추가
@mcp.resource("greeting://{name}")
def get_greeting(name: str) -> str:
    """개인화된 인사말 받기"""
    return f"안녕하세요, {name}님!"
```

</details>

<details>
<summary>.NET</summary>

다음 내용으로 Program.cs 파일을 만듭니다.

```csharp
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;
using ModelContextProtocol.Server;
using System.ComponentModel;

var builder = Host.CreateApplicationBuilder(args);
builder.Logging.AddConsole(consoleLogOptions =>
{
    // 모든 로그를 stderr로 보내도록 구성
    consoleLogOptions.LogToStandardErrorThreshold = LogLevel.Trace;
});

builder.Services
    .AddMcpServer()
    .WithStdioServerTransport()
    .WithToolsFromAssembly();
await builder.Build().RunAsync();

[McpServerToolType]
public static class CalculatorTool
{
    [McpServerTool, Description("두 숫자를 더합니다")]
    public static string Add(int a, int b) => $"합계 {a + b}";
}
```

</details>

<details>
<summary>Java</summary>

전체 기본 애플리케이션 클래스는 다음과 같아야 합니다.

```java
// McpServerApplication.java
package com.microsoft.mcp.sample.server;

import org.springframework.ai.tool.ToolCallbackProvider;
import org.springframework.ai.tool.method.MethodToolCallbackProvider;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
import com.microsoft.mcp.sample.server.service.CalculatorService;

@SpringBootApplication
public class McpServerApplication {

    public static void main(String[] args) {
        SpringApplication.run(McpServerApplication.class, args);
    }
    
    @Bean
    public ToolCallbackProvider calculatorTools(CalculatorService calculator) {
        return MethodToolCallbackProvider.builder().toolObjects(calculator).build();
    }
}
```

</details>

### -7- 서버 테스트

다음 명령으로 서버를 시작합니다.

<details>
<summary>Typescript</summary>

```sh
npm run build
```

</details>

<details>
<summary>Python</summary>

```sh
mcp run server.py
```

> MCP Inspector를 사용하려면 `mcp dev server.py`를 사용하십시오. 그러면 Inspector가 자동으로 시작되고 필요한 프록시 세션 토큰이 제공됩니다. `mcp run server.py`를 사용하는 경우 Inspector를 수동으로 시작하고 연결을 구성해야 합니다.

</details>

<details>
<summary>.NET</summary>

프로젝트 디렉토리에 있는지 확인하십시오.

```sh
cd McpCalculatorServer
dotnet run
```

</details>

<details>
<summary>Java</summary>

```bash
./mvnw clean install -DskipTests
java -jar target/calculator-server-0.0.1-SNAPSHOT.jar
```

</details>

### -8- Inspector를 사용하여 실행

Inspector는 서버를 시작하고 상호 작용하여 작동하는지 테스트할 수 있는 훌륭한 도구입니다. 시작해 보겠습니다.

> [!NOTE]
> "command" 필드에 특정 런타임으로 서버를 실행하는 명령이 포함되어 있으므로 다르게 보일 수 있습니다.

<details>
<summary>TypeScript</summary>

```sh
npx @modelcontextprotocol/inspector node build/index.js
```

또는 *package.json*에 다음과 같이 추가합니다. `"inspector": "npx @modelcontextprotocol/inspector node build/index.js"` 그런 다음 `npm run inspect`를 실행합니다.

</details>

<details>
<summary>Python</summary>

Python은 Inspector라는 Node.js 도구를 래핑합니다. 다음과 같이 해당 도구를 호출할 수 있습니다.

```sh
mcp dev server.py
```

그러나 도구에서 사용할 수 있는 모든 메서드를 구현하지는 않으므로 아래와 같이 Node.js 도구를 직접 실행하는 것이 좋습니다.

```sh
npx @modelcontextprotocol/inspector mcp run server.py
```

</details>

<details>
<summary>.NET</summary>

프로젝트 디렉토리에 있는지 확인하십시오.

```sh
cd McpCalculatorServer
npx @modelcontextprotocol/inspector dotnet run
```

</details>

<details>
<summary>Java</summary>

계산기 서버가 실행 중인지 확인하십시오.
그런 다음 Inspector를 실행합니다.

```cmd
npx @modelcontextprotocol/inspector
```

Inspector 웹 인터페이스에서:

1. 전송 유형으로 "SSE"를 선택합니다.
2. URL을 `http://localhost:8080/sse`로 설정합니다.
3. "연결"을 클릭합니다.

![연결](/03-GettingStarted/01-first-server/assets/tool.png)

**이제 서버에 연결되었습니다.**
**Java 서버 테스트 섹션이 이제 완료되었습니다.**

다음 섹션은 서버와 상호 작용하는 것입니다.

</details>

다음 사용자 인터페이스가 표시되어야 합니다.

![연결](/03-GettingStarted/01-first-server/assets/connect.png)

1. 연결 버튼을 선택하여 서버에 연결합니다.
  서버에 연결하면 이제 다음이 표시되어야 합니다.

  ![연결됨](/03-GettingStarted/01-first-server/assets/connected.png)

1. "도구" 및 "listTools"를 선택하면 "추가"가 표시되어야 합니다. "추가"를 선택하고 매개변수 값을 입력합니다.

  다음과 같은 응답이 표시되어야 합니다. 즉, "추가" 도구의 결과입니다.

  ![추가 실행 결과](/03-GettingStarted/01-first-server/assets/ran-tool.png)

축하합니다. 첫 번째 서버를 만들고 실행하는 데 성공했습니다!

### 공식 SDK

MCP는 여러 언어에 대한 공식 SDK를 제공합니다.

- [C# SDK](https://github.com/modelcontextprotocol/csharp-sdk) - Microsoft와 공동으로 유지 관리
- [Java SDK](https://github.com/modelcontextprotocol/java-sdk) - Spring AI와 공동으로 유지 관리
- [TypeScript SDK](https://github.com/modelcontextprotocol/typescript-sdk) - 공식 TypeScript 구현
- [Python SDK](https://github.com/modelcontextprotocol/python-sdk) - 공식 Python 구현
- [Kotlin SDK](https://github.com/modelcontextprotocol/kotlin-sdk) - 공식 Kotlin 구현
- [Swift SDK](https://github.com/modelcontextprotocol/swift-sdk) - Loopwork AI와 공동으로 유지 관리
- [Rust SDK](https://github.com/modelcontextprotocol/rust-sdk) - 공식 Rust 구현

## 주요 내용

- MCP 개발 환경 설정은 언어별 SDK를 사용하여 간단합니다.
- MCP 서버 구축에는 명확한 스키마로 도구를 만들고 등록하는 것이 포함됩니다.
- 테스트 및 디버깅은 신뢰할 수 있는 MCP 구현에 필수적입니다.

## 샘플

- [Java 계산기](../samples/java/calculator/README.md)
- [.Net 계산기](../samples/csharp/)
- [JavaScript 계산기](../samples/javascript/README.md)
- [TypeScript 계산기](../samples/typescript/README.md)
- [Python 계산기](../samples/python/)

## 과제

선택한 도구로 간단한 MCP 서버를 만듭니다.

1. 선호하는 언어(.NET, Java, Python 또는 JavaScript)로 도구를 구현합니다.
2. 입력 매개변수 및 반환 값을 정의합니다.
3. Inspector 도구를 실행하여 서버가 의도한 대로 작동하는지 확인합니다.
4. 다양한 입력으로 구현을 테스트합니다.

## 해결책

[해결책](./solution/README.md)

## 추가 리소스

- [Azure에서 모델 컨텍스트 프로토콜을 사용하여 에이전트 빌드](https://learn.microsoft.com/azure/developer/ai/intro-agents-mcp)
- [Azure Container Apps를 사용한 원격 MCP(Node.js/TypeScript/JavaScript)](https://learn.microsoft.com/samples/azure-samples/mcp-container-ts/mcp-container-ts/)
- [.NET OpenAI MCP 에이전트](https://learn.microsoft.com/samples/azure-samples/openai-mcp-agent-dotnet/openai-mcp-agent-dotnet/)

## 다음 내용

다음: [MCP 클라이언트 시작하기](../02-client/README.md)