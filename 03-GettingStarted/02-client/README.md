# 클라이언트 만들기

클라이언트는 MCP 서버와 직접 통신하여 리소스, 도구 및 프롬프트를 요청하는 사용자 지정 애플리케이션 또는 스크립트입니다. 서버와 상호 작용하기 위한 그래픽 인터페이스를 제공하는 검사기 도구를 사용하는 것과 달리, 자체 클라이언트를 작성하면 프로그래밍 방식 및 자동화된 상호 작용이 가능합니다. 이를 통해 개발자는 MCP 기능을 자체 워크플로에 통합하고, 작업을 자동화하고, 특정 요구에 맞는 사용자 지정 솔루션을 구축할 수 있습니다.

## 개요

이 단원에서는 모델 컨텍스트 프로토콜(MCP) 생태계 내의 클라이언트 개념을 소개합니다. 자체 클라이언트를 작성하고 MCP 서버에 연결하는 방법을 배웁니다.
 
## 학습 목표

이 단원을 마치면 다음을 수행할 수 있습니다.

- 클라이언트가 할 수 있는 일을 이해합니다.
- 자체 클라이언트를 작성합니다.
- 클라이언트를 MCP 서버에 연결하고 테스트하여 후자가 예상대로 작동하는지 확인합니다.

## 클라이언트 작성에 포함되는 내용

클라이언트를 작성하려면 다음을 수행해야 합니다.

- **올바른 라이브러리 가져오기**. 이전과 동일한 라이브러리를 사용하지만 다른 구문을 사용합니다.
- **클라이언트 인스턴스화**. 여기에는 클라이언트 인스턴스를 만들고 선택한 전송 방법에 연결하는 작업이 포함됩니다.
- **나열할 리소스 결정**. MCP 서버에는 리소스, 도구 및 프롬프트가 제공되므로 나열할 리소스를 결정해야 합니다.
- **클라이언트를 호스트 애플리케이션에 통합**. 서버의 기능을 알게 되면 사용자가 프롬프트나 다른 명령을 입력하면 해당 서버 기능이 호출되도록 호스트 애플리케이션에 통합해야 합니다.

이제 우리가 하려는 일을 높은 수준에서 이해했으므로 다음 예제를 살펴보겠습니다.

### 예제 클라이언트

이 예제 클라이언트를 살펴보겠습니다.

<details>
<summary>TypeScript</summary>

```typescript
import { Client } from "@modelcontextprotocol/sdk/client/index.js";
import { StdioClientTransport } from "@modelcontextprotocol/sdk/client/stdio.js";

const transport = new StdioClientTransport({
  command: "node",
  args: ["server.js"]
});

const client = new Client(
  {
    name: "example-client",
    version: "1.0.0"
  }
);

await client.connect(transport);

// 프롬프트 나열
const prompts = await client.listPrompts();

// 프롬프트 가져오기
const prompt = await client.getPrompt({
  name: "example-prompt",
  arguments: {
    arg1: "value"
  }
});

// 리소스 나열
const resources = await client.listResources();

// 리소스 읽기
const resource = await client.readResource({
  uri: "file:///example.txt"
});

// 도구 호출
const result = await client.callTool({
  name: "example-tool",
  arguments: {
    arg1: "value"
  }
});
```

</details>

앞의 코드에서는 다음을 수행했습니다.

- 라이브러리 가져오기
- 클라이언트 인스턴스를 만들고 전송을 위해 stdio를 사용하여 연결합니다.
- 프롬프트, 리소스 및 도구를 나열하고 모두 호출합니다.

이제 MCP 서버와 통신할 수 있는 클라이언트가 생겼습니다.

다음 연습 섹션에서 시간을 내어 각 코드 조각을 분석하고 무슨 일이 일어나고 있는지 설명하겠습니다.

## 연습: 클라이언트 작성

위에서 말했듯이 코드를 설명하는 데 시간을 할애하고 원한다면 코드를 따라 작성하십시오.

### -1- 라이브러리 가져오기

필요한 라이브러리를 가져오겠습니다. 클라이언트 및 선택한 전송 프로토콜인 stdio에 대한 참조가 필요합니다. stdio는 로컬 컴퓨터에서 실행되도록 설계된 프로토콜입니다. SSE는 향후 장에서 보여줄 또 다른 전송 프로토콜이지만 다른 옵션입니다. 하지만 지금은 stdio로 계속 진행하겠습니다.

<details>
<summary>TypeScript</summary>

```typescript
import { Client } from "@modelcontextprotocol/sdk/client/index.js";
import { StdioClientTransport } from "@modelcontextprotocol/sdk/client/stdio.js";
```

</details>

<details>
<summary>Python</summary>

```python
from mcp import ClientSession, StdioServerParameters, types
from mcp.client.stdio import stdio_client
```

</details>

<details>
<summary>.NET</summary>

```csharp
using Microsoft.Extensions.AI;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Hosting;
using ModelContextProtocol.Client;
using ModelContextProtocol.Protocol.Transport;
```

</details>

<details>
<summary>Java</summary>

Java의 경우 이전 연습에서 MCP 서버에 연결하는 클라이언트를 만듭니다. [MCP 서버 시작하기](../01-first-server/solution/java)의 동일한 Java Spring Boot 프로젝트 구조를 사용하여 `src/main/java/com/microsoft/mcp/sample/client/` 폴더에 `SDKClient`라는 새 Java 클래스를 만들고 다음 가져오기를 추가합니다.

```java
import java.util.Map;
import org.springframework.web.reactive.function.client.WebClient;
import io.modelcontextprotocol.client.McpClient;
import io.modelcontextprotocol.client.transport.WebFluxSseClientTransport;
import io.modelcontextprotocol.spec.McpClientTransport;
import io.modelcontextprotocol.spec.McpSchema.CallToolRequest;
import io.modelcontextprotocol.spec.McpSchema.CallToolResult;
import io.modelcontextprotocol.spec.McpSchema.ListToolsResult;
```

</details>

인스턴스화로 넘어 갑시다.

### -2- 클라이언트 및 전송 인스턴스화

전송 및 클라이언트 인스턴스를 만들어야 합니다.

<details>
<summary>TypeScript</summary>

```typescript
const transport = new StdioClientTransport({
  command: "node",
  args: ["server.js"]
});

const client = new Client(
  {
    name: "example-client",
    version: "1.0.0"
  }
);

await client.connect(transport);
```

앞의 코드에서는 다음을 수행했습니다.

- stdio 전송 인스턴스를 만들었습니다. 클라이언트를 만들 때 서버를 찾고 시작하는 방법을 지정하는 명령 및 인수를 지정하는 방법을 확인하십시오.

    ```typescript
    const transport = new StdioClientTransport({
        command: "node",
        args: ["server.js"]
    });
    ```

- 이름과 버전을 지정하여 클라이언트를 인스턴스화했습니다.

    ```typescript
    const client = new Client(
    {
        name: "example-client",
        version: "1.0.0"
    });
    ```

- 클라이언트를 선택한 전송에 연결했습니다.

    ```typescript
    await client.connect(transport);
    ```

</details>

<details>
<summary>Python</summary>

```python
from mcp import ClientSession, StdioServerParameters, types
from mcp.client.stdio import stdio_client

# stdio 연결을 위한 서버 매개변수 만들기
server_params = StdioServerParameters(
    command="mcp",  # 실행 파일
    args=["run", "server.py"],  # 선택적 명령줄 인수
    env=None,  # 선택적 환경 변수
)

async def run():
    async with stdio_client(server_params) as (read, write):
        async with ClientSession(
            read, write
        ) as session:
            # 연결 초기화
            await session.initialize()

          

if __name__ == "__main__":
    import asyncio

    asyncio.run(run())
```

앞의 코드에서는 다음을 수행했습니다.

- 필요한 라이브러리를 가져왔습니다.
- 클라이언트로 연결할 수 있도록 서버를 실행하는 데 사용할 서버 매개변수 개체를 인스턴스화했습니다.
- 클라이언트 세션을 시작하는 `stdio_client`를 차례로 호출하는 `run` 메서드를 정의했습니다.
- `asyncio.run`에 `run` 메서드를 제공하는 진입점을 만들었습니다.

</details>

<details>
<summary>.NET</summary>

```dotnet
using Microsoft.Extensions.AI;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Hosting;
using ModelContextProtocol.Client;
using ModelContextProtocol.Protocol.Transport;

var builder = Host.CreateApplicationBuilder(args);

builder.Configuration
    .AddEnvironmentVariables()
    .AddUserSecrets<Program>();



var clientTransport = new StdioClientTransport(new()
{
    Name = "Demo Server",
    Command = "dotnet",
    Arguments = ["run", "--project", "path/to/file.csproj"],
});

await using var mcpClient = await McpClientFactory.CreateAsync(clientTransport);
```

앞의 코드에서는 다음을 수행했습니다.

- 필요한 라이브러리를 가져왔습니다.
- stdio 전송을 만들고 클라이언트 `mcpClient`를 만들었습니다. 후자는 MCP 서버의 기능을 나열하고 호출하는 데 사용할 것입니다.

참고로 "Arguments"에서 *.csproj* 또는 실행 파일을 가리킬 수 있습니다.

</details>

<details>
<summary>Java</summary>

```java
public class SDKClient {
    
    public static void main(String[] args) {
        var transport = new WebFluxSseClientTransport(WebClient.builder().baseUrl("http://localhost:8080"));
        new SDKClient(transport).run();
    }
    
    private final McpClientTransport transport;

    public SDKClient(McpClientTransport transport) {
        this.transport = transport;
    }

    public void run() {
        var client = McpClient.sync(this.transport).build();
        client.initialize();
        
        // 여기에 클라이언트 로직을 작성합니다.
    }
}
```

앞의 코드에서는 다음을 수행했습니다.

- MCP 서버가 실행될 `http://localhost:8080`을 가리키는 SSE 전송을 설정하는 기본 메서드를 만들었습니다.
- 전송을 생성자 매개변수로 사용하는 클라이언트 클래스를 만들었습니다.
- `run` 메서드에서 전송을 사용하여 동기 MCP 클라이언트를 만들고 연결을 초기화합니다.
- Java Spring Boot MCP 서버와 HTTP 기반 통신에 적합한 SSE(Server-Sent Events) 전송을 사용했습니다.

</details>

### -3- 서버 기능 나열

이제 프로그램이 실행되어야 연결할 수 있는 클라이언트가 있습니다. 그러나 실제로는 기능을 나열하지 않으므로 다음에 수행하겠습니다.

<details>
<summary>TypeScript</summary>

```typescript
// 프롬프트 나열
const prompts = await client.listPrompts();

// 리소스 나열
const resources = await client.listResources();

// 도구 나열
const tools = await client.listTools();
```

</details>

<details>
<summary>Python</summary>

```python
# 사용 가능한 리소스 나열
resources = await session.list_resources();
print("리소스 나열");
for resource in resources:
    print("리소스: ", resource);

# 사용 가능한 도구 나열
tools = await session.list_tools();
print("도구 나열");
for tool in tools.tools:
    print("도구: ", tool.name);
```

여기서는 사용 가능한 리소스, `list_resources()` 및 도구, `list_tools`를 나열하고 출력합니다.

</details>

<details>
<summary>.NET</summary>

```dotnet
foreach (var tool in await client.ListToolsAsync())
{
    Console.WriteLine($"{tool.Name} ({tool.Description})");
}
```

위는 서버의 도구를 나열하는 방법의 예입니다. 각 도구에 대해 이름을 출력합니다.

</details>

<details>
<summary>Java</summary>

```java
// 도구 나열 및 시연
ListToolsResult toolsList = client.listTools();
System.out.println("사용 가능한 도구 = " + toolsList);

// 서버에 핑을 보내 연결을 확인할 수도 있습니다.
client.ping();
```

앞의 코드에서는 다음을 수행했습니다.

- MCP 서버에서 사용 가능한 모든 도구를 가져오기 위해 `listTools()`를 호출했습니다.
- 서버에 대한 연결이 작동하는지 확인하기 위해 `ping()`을 사용했습니다.
- `ListToolsResult`에는 이름, 설명 및 입력 스키마를 포함한 모든 도구에 대한 정보가 포함되어 있습니다.

</details>

좋습니다. 이제 모든 기능을 캡처했습니다. 이제 문제는 언제 사용하느냐입니다. 음, 이 클라이언트는 매우 간단합니다. 간단하다는 의미는 우리가 원할 때 기능을 명시적으로 호출해야 한다는 것입니다. 다음 장에서는 자체 대규모 언어 모델인 LLM에 액세스할 수 있는 더 고급 클라이언트를 만들 것입니다. 하지만 지금은 서버에서 기능을 호출하는 방법을 살펴보겠습니다.

### -4- 기능 호출

기능을 호출하려면 올바른 인수를 지정하고 경우에 따라 호출하려는 항목의 이름을 지정해야 합니다.

<details>
<summary>TypeScript</summary>

```typescript

// 리소스 읽기
const resource = await client.readResource({
  uri: "file:///example.txt"
});

// 도구 호출
const result = await client.callTool({
  name: "example-tool",
  arguments: {
    arg1: "value"
  }
});

// 프롬프트 호출
const promptResult = await client.getPrompt({
    name: "review-code",
    arguments: {
        code: "console.log(\"Hello world\")"
    }
})
```

앞의 코드에서는 다음을 수행했습니다.

- 리소스를 읽고, `uri`를 지정하여 `readResource()`를 호출하여 리소스를 호출합니다. 서버 측에서는 다음과 같이 보일 가능성이 높습니다.

    ```typescript
    server.resource(
        "readFile",
        new ResourceTemplate("file://{name}", { list: undefined }),
        async (uri, { name }) => ({
          contents: [{
            uri: uri.href,
            text: `안녕하세요, ${name}님!`
          }]
        })
    );
    ```

    `uri` 값 `file://example.txt`는 서버의 `file://{name}`과 일치합니다. `example.txt`는 `name`에 매핑됩니다.

- 도구를 호출하고, `name`과 `arguments`를 다음과 같이 지정하여 호출합니다.

    ```typescript
    const result = await client.callTool({
        name: "example-tool",
        arguments: {
            arg1: "value"
        }
    });
    ```

- 프롬프트를 가져오고, `name`과 `arguments`를 사용하여 `getPrompt()`를 호출합니다. 서버 코드는 다음과 같습니다.

    ```typescript
    server.prompt(
        "review-code",
        { code: z.string() },
        ({ code }) => ({
            messages: [{
            role: "user",
            content: {
                type: "text",
                text: `이 코드를 검토해 주세요:\n\n${code}`
            }
            }]
        })
    );
    ```

    따라서 결과 클라이언트 코드는 서버에 선언된 것과 일치하도록 다음과 같이 보입니다.

    ```typescript
    const promptResult = await client.getPrompt({
        name: "review-code",
        arguments: {
            code: "console.log(\"Hello world\")"
        }
    })
    ```

</details>

<details>
<summary>Python</summary>

```python
# 리소스 읽기
print("리소스 읽기");
content, mime_type = await session.read_resource("greeting://hello");

# 도구 호출
print("도구 호출");
result = await session.call_tool("add", arguments={"a": 1, "b": 7});
print(result.content);
```

앞의 코드에서는 다음을 수행했습니다.

- `read_resource`를 사용하여 `greeting`이라는 리소스를 호출했습니다.
- `call_tool`을 사용하여 `add`라는 도구를 호출했습니다.

</details>

<details>
<summary>C#</summary>

1. 도구를 호출하는 코드를 추가해 보겠습니다.

  ```csharp
  var result = await mcpClient.CallToolAsync(
      "Add",
      new Dictionary<string, object?>() { ["a"] = 1, ["b"] = 3  },
      cancellationToken:CancellationToken.None);
  ```

1. 결과를 출력하려면 다음 코드를 처리합니다.

  ```csharp
  Console.WriteLine(result.Content.First(c => c.Type == "text").Text);
  // 합계 4
  ```

</details>

<details>
<summary>Java</summary>

```java
// 다양한 계산기 도구 호출
CallToolResult resultAdd = client.callTool(new CallToolRequest("add", Map.of("a", 5.0, "b", 3.0)));
System.out.println("더하기 결과 = " + resultAdd);

CallToolResult resultSubtract = client.callTool(new CallToolRequest("subtract", Map.of("a", 10.0, "b", 4.0)));
System.out.println("빼기 결과 = " + resultSubtract);

CallToolResult resultMultiply = client.callTool(new CallToolRequest("multiply", Map.of("a", 6.0, "b", 7.0)));
System.out.println("곱하기 결과 = " + resultMultiply);

CallToolResult resultDivide = client.callTool(new CallToolRequest("divide", Map.of("a", 20.0, "b", 4.0)));
System.out.println("나누기 결과 = " + resultDivide);

CallToolResult resultHelp = client.callTool(new CallToolRequest("help", Map.of()));
System.out.println("도움말 = " + resultHelp);
```

앞의 코드에서는 다음을 수행했습니다.

- `CallToolRequest` 개체를 사용하여 `callTool()` 메서드로 여러 계산기 도구를 호출했습니다.
- 각 도구 호출은 도구 이름과 해당 도구에 필요한 인수 `Map`을 지정합니다.
- 서버 도구는 특정 매개변수 이름(예: 수학 연산의 경우 "a", "b")을 예상합니다.
- 결과는 서버의 응답을 포함하는 `CallToolResult` 개체로 반환됩니다.

</details>

### -5- 클라이언트 실행

터미널에 다음 명령을 입력하여 클라이언트를 실행합니다.

<details>
<summary>TypeScript</summary>

*package.json*의 "scripts" 섹션에 다음 항목을 추가합니다.

```json
"client": "tsx && node build/client.js"
```

```sh
npm run client
```

</details>

<details>
<summary>Python</summary>

다음 명령으로 클라이언트를 호출합니다.

```sh
python client.py
```

</details>

<details>
<summary>.NET</summary>

```sh
dotnet run
```

</details>

<details>
<summary>Java</summary>

먼저 MCP 서버가 `http://localhost:8080`에서 실행 중인지 확인합니다. 그런 다음 클라이언트를 실행합니다.

```bash
# 프로젝트 빌드
./mvnw clean compile

# 클라이언트 실행
./mvnw exec:java -Dexec.mainClass="com.microsoft.mcp.sample.client.SDKClient"
```

또는 솔루션 폴더 `03-GettingStarted\02-client\solution\java`에 제공된 전체 클라이언트 프로젝트를 실행할 수 있습니다.

```bash
# 솔루션 디렉토리로 이동
cd 03-GettingStarted/02-client/solution/java

# JAR 빌드 및 실행
./mvnw clean package
java -jar target/calculator-client-0.0.1-SNAPSHOT.jar
```

</details>

## 과제

이 과제에서는 클라이언트를 만드는 데 배운 내용을 사용하여 자신만의 클라이언트를 만듭니다.

다음은 클라이언트 코드를 통해 호출해야 하는 서버입니다. 서버에 더 많은 기능을 추가하여 더 흥미롭게 만들 수 있는지 확인하십시오.

<details>
<summary>TypeScript</summary>

```typescript
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

async function main() {
  const transport = new StdioServerTransport();
  await server.connect(transport);
  console.error("MCPServer started on stdin/stdout");
}

main().catch((error) => {
  console.error("치명적인 오류: ", error);
  process.exit(1);
});
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

이 프로젝트를 참조하여 [프롬프트 및 리소스 추가 방법](https://github.com/modelcontextprotocol/csharp-sdk/blob/main/samples/EverythingServer/Program.cs)을 확인하십시오.

또한 [프롬프트 및 리소스 호출 방법](https://github.com/modelcontextprotocol/csharp-sdk/blob/main/src/ModelContextProtocol/Client/)에 대한 이 링크를 확인하십시오.

</details>


## 해결책

[해결책](./solution/README.md)

## 주요 내용

이 장의 주요 내용은 클라이언트에 대한 다음과 같습니다.

- 서버의 기능을 검색하고 호출하는 데 사용할 수 있습니다.
- 이 장에서와 같이 자체적으로 시작하는 동안 서버를 시작할 수 있지만 클라이언트는 실행 중인 서버에도 연결할 수 있습니다.
- 이전 장에서 설명한 Inspector와 같은 대안 옆에 서버 기능을 테스트하는 좋은 방법입니다.

## 추가 리소스

- [MCP에서 클라이언트 구축](https://modelcontextprotocol.io/quickstart/client)

## 샘플

- [Java 계산기](../samples/java/calculator/README.md)
- [.Net 계산기](../samples/csharp/)
- [JavaScript 계산기](../samples/javascript/README.md)
- [TypeScript 계산기](../samples/typescript/README.md)
- [Python 계산기](../samples/python/) 

## 다음 내용

- 다음: [LLM으로 클라이언트 만들기](../03-llm-client/README.md)