# LLM으로 클라이언트 만들기

지금까지 서버와 클라이언트를 만드는 방법을 보았습니다. 클라이언트는 서버의 도구, 리소스 및 프롬프트를 나열하기 위해 명시적으로 서버를 호출할 수 있었습니다. 그러나 이는 그다지 실용적인 접근 방식이 아닙니다. 사용자는 에이전트 시대에 살고 있으며 프롬프트를 사용하고 LLM과 통신하여 그렇게 할 것으로 기대합니다. 사용자는 기능을 저장하기 위해 MCP를 사용하는지 여부에 관계없이 자연어를 사용하여 상호 작용할 것으로 기대합니다. 그렇다면 이 문제를 어떻게 해결할 수 있을까요? 해결책은 클라이언트에 LLM을 추가하는 것입니다.

## 개요

이 단원에서는 클라이언트에 LLM을 추가하는 데 중점을 두고 이것이 사용자에게 훨씬 더 나은 경험을 제공하는 방법을 보여줍니다.

## 학습 목표

이 단원을 마치면 다음을 수행할 수 있습니다.

- LLM으로 클라이언트를 만듭니다.
- LLM을 사용하여 MCP 서버와 원활하게 상호 작용합니다.
- 클라이언트 측에서 더 나은 최종 사용자 경험을 제공합니다.

## 접근 방식

우리가 취해야 할 접근 방식을 이해해 봅시다. LLM을 추가하는 것은 간단하게 들리지만 실제로 이것을 할 수 있을까요?

클라이언트가 서버와 상호 작용하는 방법은 다음과 같습니다.

1. 서버와 연결을 설정합니다.

1. 기능, 프롬프트, 리소스 및 도구를 나열하고 스키마를 저장합니다.

1. LLM을 추가하고 저장된 기능과 해당 스키마를 LLM이 이해하는 형식으로 전달합니다.

1. 클라이언트가 나열한 도구와 함께 LLM에 전달하여 사용자 프롬프트를 처리합니다.

좋습니다. 이제 높은 수준에서 이 작업을 수행하는 방법을 이해했으므로 아래 연습에서 시도해 보겠습니다.

## 연습: LLM으로 클라이언트 만들기

이 연습에서는 클라이언트에 LLM을 추가하는 방법을 배웁니다.

## GitHub 개인용 액세스 토큰을 사용한 인증

GitHub 토큰을 만드는 것은 간단한 과정입니다. 방법은 다음과 같습니다.

- GitHub 설정으로 이동 – 오른쪽 상단 모서리에 있는 프로필 사진을 클릭하고 설정을 선택합니다.
- 개발자 설정으로 이동 – 아래로 스크롤하여 개발자 설정을 클릭합니다.
- 개인용 액세스 토큰 선택 – 개인용 액세스 토큰을 클릭한 다음 새 토큰 생성을 클릭합니다.
- 토큰 구성 – 참조용 메모를 추가하고 만료 날짜를 설정하고 필요한 범위(권한)를 선택합니다.
- 토큰 생성 및 복사 – 토큰 생성을 클릭하고 다시 볼 수 없으므로 즉시 복사해야 합니다.

### -1- 서버에 연결

먼저 클라이언트를 만들어 보겠습니다.

<details>
<summary>Typescript</summary>

```typescript
import { Client } from "@modelcontextprotocol/sdk/client/index.js";
import { StdioClientTransport } from "@modelcontextprotocol/sdk/client/stdio.js";
import { Transport } from "@modelcontextprotocol/sdk/shared/transport.js";
import OpenAI from "openai";
import { z } from "zod"; // 스키마 유효성 검사를 위해 zod 가져오기

class MCPClient {
    private openai: OpenAI;
    private client: Client;
    constructor(){
        this.openai = new OpenAI({
            baseURL: "https://models.inference.ai.azure.com", 
            apiKey: process.env.GITHUB_TOKEN,
        });

        this.client = new Client(
            {
                name: "example-client",
                version: "1.0.0"
            },
            {
                capabilities: {
                prompts: {},
                resources: {},
                tools: {}
                }
            }
            );    
    }
}
```

앞의 코드에서는 다음을 수행했습니다.

- 필요한 라이브러리를 가져왔습니다.
- 클라이언트를 관리하고 각각 LLM과 상호 작용하는 데 도움이 되는 두 멤버 `client`와 `openai`가 있는 클래스를 만들었습니다.
- `baseUrl`을 추론 API를 가리키도록 설정하여 GitHub 모델을 사용하도록 LLM 인스턴스를 구성했습니다.

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

- MCP에 필요한 라이브러리를 가져왔습니다.
- 클라이언트를 만들었습니다.

</details>

<details>
<summary>.NET</summary>

```csharp
using Azure;
using Azure.AI.Inference;
using Azure.Identity;
using System.Text.Json;
using ModelContextProtocol.Client;
using ModelContextProtocol.Protocol.Transport;
using System.Text.Json;

var clientTransport = new StdioClientTransport(new()
{
    Name = "데모 서버",
    Command = "/workspaces/mcp-for-beginners/03-GettingStarted/02-client/solution/server/bin/Debug/net8.0/server",
    Arguments = [],
});

await using var mcpClient = await McpClientFactory.CreateAsync(clientTransport);
```

</details>

<details>
<summary>Java</summary>

먼저 `pom.xml` 파일에 LangChain4j 종속성을 추가해야 합니다. MCP 통합 및 GitHub 모델 지원을 활성화하려면 이러한 종속성을 추가하십시오.

```xml
<properties>
    <langchain4j.version>1.0.0-beta3</langchain4j.version>
</properties>

<dependencies>
    <!-- LangChain4j MCP 통합 -->
    <dependency>
        <groupId>dev.langchain4j</groupId>
        <artifactId>langchain4j-mcp</artifactId>
        <version>${langchain4j.version}</version>
    </dependency>
    
    <!-- OpenAI 공식 API 클라이언트 -->
    <dependency>
        <groupId>dev.langchain4j</groupId>
        <artifactId>langchain4j-open-ai-official</artifactId>
        <version>${langchain4j.version}</version>
    </dependency>
    
    <!-- GitHub 모델 지원 -->
    <dependency>
        <groupId>dev.langchain4j</groupId>
        <artifactId>langchain4j-github-models</artifactId>
        <version>${langchain4j.version}</version>
    </dependency>
    
    <!-- Spring Boot 스타터 (프로덕션 앱의 경우 선택 사항) -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
</dependencies>
```

그런 다음 Java 클라이언트 클래스를 만듭니다.

```java
import dev.langchain4j.mcp.McpToolProvider;
import dev.langchain4j.mcp.client.DefaultMcpClient;
import dev.langchain4j.mcp.client.McpClient;
import dev.langchain4j.mcp.client.transport.McpTransport;
import dev.langchain4j.mcp.client.transport.http.HttpMcpTransport;
import dev.langchain4j.model.chat.ChatLanguageModel;
import dev.langchain4j.model.openaiofficial.OpenAiOfficialChatModel;
import dev.langchain4j.service.AiServices;
import dev.langchain4j.service.tool.ToolProvider;

import java.time.Duration;
import java.util.List;

public class LangChain4jClient {
    
    public static void main(String[] args) throws Exception {        // GitHub 모델을 사용하도록 LLM 구성
        ChatLanguageModel model = OpenAiOfficialChatModel.builder()
                .isGitHubModels(true)
                .apiKey(System.getenv("GITHUB_TOKEN"))
                .timeout(Duration.ofSeconds(60))
                .modelName("gpt-4.1-nano")
                .build();

        // 서버 연결을 위한 MCP 전송 만들기
        McpTransport transport = new HttpMcpTransport.Builder()
                .sseUrl("http://localhost:8080/sse")
                .timeout(Duration.ofSeconds(60))
                .logRequests(true)
                .logResponses(true)
                .build();

        // MCP 클라이언트 만들기
        McpClient mcpClient = new DefaultMcpClient.Builder()
                .transport(transport)
                .build();
    }
}
```

앞의 코드에서는 다음을 수행했습니다.

- **LangChain4j 종속성 추가**: MCP 통합, OpenAI 공식 클라이언트 및 GitHub 모델 지원에 필요합니다.
- **LangChain4j 라이브러리 가져오기**: MCP 통합 및 OpenAI 채팅 모델 기능을 위해
- **`ChatLanguageModel` 만들기**: GitHub 토큰으로 GitHub 모델을 사용하도록 구성되었습니다.
- **HTTP 전송 설정**: MCP 서버에 연결하기 위해 서버 전송 이벤트(SSE)를 사용합니다.
- **MCP 클라이언트 만들기**: 서버와의 통신을 처리합니다.
- **LangChain4j의 내장 MCP 지원 사용**: LLM과 MCP 서버 간의 통합을 단순화합니다.

</details>


좋습니다. 다음 단계에서는 서버의 기능을 나열해 보겠습니다.

### -2 서버 기능 나열

이제 서버에 연결하여 해당 기능을 요청합니다.

<details>
<summary>Typescript</summary>

동일한 클래스에 다음 메서드를 추가합니다.

```typescript
async connectToServer(transport: Transport) {
     await this.client.connect(transport);
     this.run();
     console.error("MCPClient started on stdin/stdout");
}

async run() {
    console.log("서버에 사용 가능한 도구 요청 중");

    // 도구 나열
    const toolsResult = await this.client.listTools();
}
```

앞의 코드에서는 다음을 수행했습니다.

- 서버에 연결하기 위한 코드 `connectToServer`를 추가했습니다.
- 앱 흐름을 처리하는 `run` 메서드를 만들었습니다. 지금까지는 도구만 나열하지만 곧 더 추가할 것입니다.

</details>

<details>
<summary>Python</summary>

```python
# 사용 가능한 리소스 나열
resources = await session.list_resources()
print("리소스 나열")
for resource in resources:
    print("리소스: ", resource)

# 사용 가능한 도구 나열
tools = await session.list_tools()
print("도구 나열")
for tool in tools.tools:
    print("도구: ", tool.name)
    print("도구", tool.inputSchema["properties"])
```

추가한 내용은 다음과 같습니다.

- 리소스와 도구를 나열하고 출력했습니다. 도구의 경우 나중에 사용할 `inputSchema`도 나열합니다.

</details>

<details>
<summary>.NET</summary>

```csharp
async Task<List<ChatCompletionsToolDefinition>> GetMcpTools()
{
    Console.WriteLine("도구 나열");
    var tools = await mcpClient.ListToolsAsync();

    List<ChatCompletionsToolDefinition> toolDefinitions = new List<ChatCompletionsToolDefinition>();

    foreach (var tool in tools)
    {
        Console.WriteLine($"도구가 있는 서버에 연결됨: {tool.Name}");
        Console.WriteLine($"도구 설명: {tool.Description}");
        Console.WriteLine($"도구 매개변수: {tool.JsonSchema}");

        // TODO: MCP 도구에서 LLm 도구로 도구 정의 변환     
    }

    return toolDefinitions;
}
```

앞의 코드에서는 다음을 수행했습니다.

- MCP 서버에서 사용 가능한 도구를 나열했습니다.
- 각 도구에 대해 이름, 설명 및 해당 스키마를 나열했습니다. 후자는 곧 도구를 호출하는 데 사용할 것입니다.

</details>

<details>
<summary>Java</summary>

```java
// MCP 도구를 자동으로 검색하는 도구 공급자 만들기
ToolProvider toolProvider = McpToolProvider.builder()
        .mcpClients(List.of(mcpClient))
        .build();

// MCP 도구 공급자는 다음을 자동으로 처리합니다.
// - MCP 서버에서 사용 가능한 도구 나열
// - MCP 도구 스키마를 LangChain4j 형식으로 변환
// - 도구 실행 및 응답 관리
```

앞의 코드에서는 다음을 수행했습니다.

- MCP 서버의 모든 도구를 자동으로 검색하고 등록하는 `McpToolProvider`를 만들었습니다.
- 도구 공급자는 MCP 도구 스키마와 LangChain4j의 도구 형식 간의 변환을 내부적으로 처리합니다.
- 이 접근 방식은 수동 도구 나열 및 변환 프로세스를 추상화합니다.

</details>


### -3- 서버 기능을 LLM 도구로 변환

서버 기능을 나열한 후 다음 단계는 LLM이 이해하는 형식으로 변환하는 것입니다. 그렇게 하면 이러한 기능을 LLM에 도구로 제공할 수 있습니다.

<details>
<summary>Typescript</summary>

1. MCP 서버의 응답을 LLM이 사용할 수 있는 도구 형식으로 변환하는 다음 코드를 추가합니다.

    ```typescript
    openAiToolAdapter(tool: {
        name: string;
        description?: string;
        input_schema: any;
        }) {
        // input_schema를 기반으로 zod 스키마 만들기
        const schema = z.object(tool.input_schema);
    
        return {
            type: "function" as const, // 유형을 "function"으로 명시적으로 설정
            function: {
            name: tool.name,
            description: tool.description,
            parameters: {
            type: "object",
            properties: tool.input_schema.properties,
            required: tool.input_schema.required,
            },
            },
        };
    }

    ```

    위 코드는 MCP 서버의 응답을 가져와 LLM이 이해할 수 있는 도구 정의 형식으로 변환합니다.

1. 다음으로 `run` 메서드를 업데이트하여 서버 기능을 나열합니다.

    ```typescript
    async run() {
        console.log("서버에 사용 가능한 도구 요청 중");
        const toolsResult = await this.client.listTools();
        const tools = toolsResult.tools.map((tool) => {
            return this.openAiToolAdapter({
            name: tool.name,
            description: tool.description,
            input_schema: tool.inputSchema,
            });
        });
    }
    ```

    앞의 코드에서는 `run` 메서드를 업데이트하여 결과를 매핑하고 각 항목에 대해 `openAiToolAdapter`를 호출합니다.

</details>

<details>
<summary>Python</summary>

1. 먼저 다음 변환기 함수를 만듭니다.

    ```python
    def convert_to_llm_tool(tool):
        tool_schema = {
            "type": "function",
            "function": {
                "name": tool.name,
                "description": tool.description,
                "type": "function",
                "parameters": {
                    "type": "object",
                    "properties": tool.inputSchema["properties"]
                }
            }
        }

        return tool_schema
    ```

    위의 `convert_to_llm_tools` 함수에서는 MCP 도구 응답을 가져와 LLM이 이해할 수 있는 형식으로 변환합니다.

1. 다음으로 클라이언트 코드를 업데이트하여 이 함수를 다음과 같이 활용합니다.

    ```python
    for tool in tools.tools:
        print("도구: ", tool.name)
        print("도구", tool.inputSchema["properties"])
        functions.append(convert_to_llm_tool(tool))
    ```

    여기서는 `convert_to_llm_tool`에 대한 호출을 추가하여 MCP 도구 응답을 나중에 LLM에 제공할 수 있는 것으로 변환합니다.

</details>

<details>
<summary>.NET</summary>

1. MCP 도구 응답을 LLM이 이해할 수 있는 것으로 변환하는 코드를 추가해 보겠습니다.

    ```csharp
    ChatCompletionsToolDefinition ConvertFrom(string name, string description, JsonElement jsonElement)
    { 
        // 도구를 함수 정의로 변환
        FunctionDefinition functionDefinition = new FunctionDefinition(name)
        {
            Description = description,
            Parameters = BinaryData.FromObjectAsJson(new
            {
                Type = "object",
                Properties = jsonElement
            },
            new JsonSerializerOptions() { PropertyNamingPolicy = JsonNamingPolicy.CamelCase })
        };

        // 도구 정의 만들기
        ChatCompletionsToolDefinition toolDefinition = new ChatCompletionsToolDefinition(functionDefinition);
        return toolDefinition;
    }
    ```

    앞의 코드에서는 다음을 수행했습니다.

    - 이름, 설명 및 입력 스키마를 사용하는 함수 `ConvertFrom`을 만들었습니다.
    - LLM이 이해할 수 있는 ChatCompletionsDefinition에 전달되는 FunctionDefinition을 만드는 기능을 정의했습니다.

1. 기존 코드를 업데이트하여 위 함수를 활용하는 방법을 살펴보겠습니다.

    ```csharp
    async Task<List<ChatCompletionsToolDefinition>> GetMcpTools()
    {
        Console.WriteLine("도구 나열");
        var tools = await mcpClient.ListToolsAsync();

        List<ChatCompletionsToolDefinition> toolDefinitions = new List<ChatCompletionsToolDefinition>();

        foreach (var tool in tools)
        {
            Console.WriteLine($"도구가 있는 서버에 연결됨: {tool.Name}");
            Console.WriteLine($"도구 설명: {tool.Description}");
            Console.WriteLine($"도구 매개변수: {tool.JsonSchema}");

            JsonElement propertiesElement;
            tool.JsonSchema.TryGetProperty("properties", out propertiesElement);

            var def = ConvertFrom(tool.Name, tool.Description, propertiesElement);
            Console.WriteLine($"도구 정의: {def}");
            toolDefinitions.Add(def);

            Console.WriteLine($"속성: {propertiesElement}");        
        }

        return toolDefinitions;
    }
    ```    앞의 코드에서는 다음을 수행했습니다.

    - MCP 도구 응답을 LLm 도구로 변환하도록 함수를 업데이트했습니다. 추가한 코드를 강조 표시해 보겠습니다.

        ```csharp
        JsonElement propertiesElement;
        tool.JsonSchema.TryGetProperty("properties", out propertiesElement);

        var def = ConvertFrom(tool.Name, tool.Description, propertiesElement);
        Console.WriteLine($"도구 정의: {def}");
        toolDefinitions.Add(def);
        ```

        입력 스키마는 도구 응답의 일부이지만 "properties" 속성에 있으므로 추출해야 합니다. 또한 이제 도구 세부 정보와 함께 `ConvertFrom`을 호출합니다. 이제 힘든 작업을 마쳤으므로 다음으로 사용자 프롬프트를 처리할 때 모든 것이 어떻게 함께 작동하는지 살펴보겠습니다.

</details>

<details>
<summary>Java</summary>

```java
// 자연어 상호 작용을 위한 봇 인터페이스 만들기
public interface Bot {
    String chat(String prompt);
}

// LLM 및 MCP 도구로 AI 서비스 구성
Bot bot = AiServices.builder(Bot.class)
        .chatLanguageModel(model)
        .toolProvider(toolProvider)
        .build();
```

앞의 코드에서는 다음을 수행했습니다.

- 자연어 상호 작용을 위한 간단한 `Bot` 인터페이스를 정의했습니다.
- LangChain4j의 `AiServices`를 사용하여 LLM을 MCP 도구 공급자와 자동으로 바인딩했습니다.
- 프레임워크는 도구 스키마 변환 및 함수 호출을 백그라운드에서 자동으로 처리합니다.
- 이 접근 방식은 수동 도구 변환을 제거합니다. LangChain4j는 MCP 도구를 LLM 호환 형식으로 변환하는 모든 복잡성을 처리합니다.

</details>

좋습니다. 이제 사용자 요청을 처리할 준비가 되었으므로 다음에 처리하겠습니다.

### -4- 사용자 프롬프트 요청 처리

이 코드 부분에서는 사용자 요청을 처리합니다.

<details>
<summary>Typescript</summary>

1. LLM을 호출하는 데 사용할 메서드를 추가합니다.

    ```typescript
    async callTools(
        tool_calls: OpenAI.Chat.Completions.ChatCompletionMessageToolCall[],
        toolResults: any[]
    ) {
        for (const tool_call of tool_calls) {
        const toolName = tool_call.function.name;
        const args = tool_call.function.arguments;

        console.log(`인수 ${JSON.stringify(args)}로 도구 ${toolName} 호출 중`);


        // 2. 서버의 도구 호출 
        const toolResult = await this.client.callTool({
            name: toolName,
            arguments: JSON.parse(args),
        });

        console.log("도구 결과: ", toolResult);

        // 3. 결과로 무언가 수행
        // TODO  

        }
    }
    ```

    앞의 코드에서는 다음을 수행했습니다.

    - `callTools` 메서드를 추가했습니다.
    - 이 메서드는 LLM 응답을 가져와 호출된 도구가 있는지 확인합니다.

        ```typescript
        for (const tool_call of tool_calls) {
        const toolName = tool_call.function.name;
        const args = tool_call.function.arguments;

        console.log(`인수 ${JSON.stringify(args)}로 도구 ${toolName} 호출 중`);

        // 도구 호출
        }
        ```

    - LLM이 호출해야 한다고 표시하면 도구를 호출합니다.

        ```typescript
        // 2. 서버의 도구 호출 
        const toolResult = await this.client.callTool({
            name: toolName,
            arguments: JSON.parse(args),
        });

        console.log("도구 결과: ", toolResult);

        // 3. 결과로 무언가 수행
        // TODO  
        ```

1. LLM에 대한 호출과 `callTools` 호출을 포함하도록 `run` 메서드를 업데이트합니다.

    ```typescript

    // 1. LLM에 대한 입력인 메시지 만들기
    const prompt = "2와 3의 합은 얼마입니까?"

    const messages: OpenAI.Chat.Completions.ChatCompletionMessageParam[] = [
            {
                role: "user",
                content: prompt,
            },
        ];

    console.log("LLM 쿼리 중: ", messages[0].content);

    // 2. LLM 호출
    let response = this.openai.chat.completions.create({
        model: "gpt-4o-mini",
        max_tokens: 1000,
        messages,
        tools: tools,
    });    

    let results: any[] = [];

    // 3. LLM 응답을 살펴보고 각 선택에 대해 도구 호출이 있는지 확인합니다. 
    (await response).choices.map(async (choice: { message: any; }) => {
        const message = choice.message;
        if (message.tool_calls) {
            console.log("도구 호출 중")
            await this.callTools(message.tool_calls, results);
        }
    });
    ```

좋습니다. 코드를 전체적으로 나열해 보겠습니다.

```typescript
import { Client } from "@modelcontextprotocol/sdk/client/index.js";
import { StdioClientTransport } from "@modelcontextprotocol/sdk/client/stdio.js";
import { Transport } from "@modelcontextprotocol/sdk/shared/transport.js";
import OpenAI from "openai";
import { z } from "zod"; // 스키마 유효성 검사를 위해 zod 가져오기

class MyClient {
    private openai: OpenAI;
    private client: Client;
    constructor(){
        this.openai = new OpenAI({
            baseURL: "https://models.inference.ai.azure.com", // 향후 이 URL로 변경해야 할 수 있습니다: https://models.github.ai/inference
            apiKey: process.env.GITHUB_TOKEN,
        });

       
        
        this.client = new Client(
            {
                name: "example-client",
                version: "1.0.0"
            },
            {
                capabilities: {
                prompts: {},
                resources: {},
                tools: {}
                }
            }
            );    
    }

    async connectToServer(transport: Transport) {
        await this.client.connect(transport);
        this.run();
        console.error("MCPClient started on stdin/stdout");
    }

    openAiToolAdapter(tool: {
        name: string;
        description?: string;
        input_schema: any;
          }) {
          // input_schema를 기반으로 zod 스키마 만들기
          const schema = z.object(tool.input_schema);
      
          return {
            type: "function" as const, // 유형을 "function"으로 명시적으로 설정
            function: {
              name: tool.name,
              description: tool.description,
              parameters: {
              type: "object",
              properties: tool.input_schema.properties,
              required: tool.input_schema.required,
              },
            },
          };
    }
    
    async callTools(
        tool_calls: OpenAI.Chat.Completions.ChatCompletionMessageToolCall[],
        toolResults: any[]
      ) {
        for (const tool_call of tool_calls) {
          const toolName = tool_call.function.name;
          const args = tool_call.function.arguments;
    
          console.log(`인수 ${JSON.stringify(args)}로 도구 ${toolName} 호출 중`);
    
    
          // 2. 서버의 도구 호출 
          const toolResult = await this.client.callTool({
            name: toolName,
            arguments: JSON.parse(args),
          });
    
          console.log("도구 결과: ", toolResult);
    
          // 3. 결과로 무언가 수행
          // TODO  
    
         }
    }

    async run() {
        console.log("서버에 사용 가능한 도구 요청 중");
        const toolsResult = await this.client.listTools();
        const tools = toolsResult.tools.map((tool) => {
            return this.openAiToolAdapter({
              name: tool.name,
              description: tool.description,
              input_schema: tool.inputSchema,
            });
        });

        const prompt = "2와 3의 합은 얼마입니까?";
    
        const messages: OpenAI.Chat.Completions.ChatCompletionMessageParam[] = [
            {
                role: "user",
                content: prompt,
            },
        ];

        console.log("LLM 쿼리 중: ", messages[0].content);
        let response = this.openai.chat.completions.create({
            model: "gpt-4o-mini",
            max_tokens: 1000,
            messages,
            tools: tools,
        });    

        let results: any[] = [];
    
        // 1. LLM 응답을 살펴보고 각 선택에 대해 도구 호출이 있는지 확인합니다. 
        (await response).choices.map(async (choice: { message: any; }) => {
          const message = choice.message;
          if (message.tool_calls) {
              console.log("도구 호출 중")
              await this.callTools(message.tool_calls, results);
          }
        });
    }
    
}

let client = new MyClient();
 const transport = new StdioClientTransport({
            command: "node",
            args: ["./build/index.js"]
        });

client.connectToServer(transport);
```

</details>

<details>
<summary>Python</summary>

1. LLM을 호출하는 데 필요한 몇 가지 가져오기를 추가해 보겠습니다.

    ```python
    # llm
    import os
    from azure.ai.inference import ChatCompletionsClient
    from azure.ai.inference.models import SystemMessage, UserMessage
    from azure.core.credentials import AzureKeyCredential
    import json
    ```

1. 다음으로 LLM을 호출할 함수를 추가합니다.

    ```python
    # llm

    def call_llm(prompt, functions):
        token = os.environ["GITHUB_TOKEN"]
        endpoint = "https://models.inference.ai.azure.com"

        model_name = "gpt-4o"

        client = ChatCompletionsClient(
            endpoint=endpoint,
            credential=AzureKeyCredential(token),
        )

        print("LLM 호출 중")
        response = client.complete(
            messages=[
                {
                "role": "system",
                "content": "당신은 도움이 되는 조수입니다.",
                },
                {
                "role": "user",
                "content": prompt,
                },
            ],
            model=model_name,
            tools = functions,
            # 선택적 매개변수
            temperature=1.,
            max_tokens=1000,
            top_p=1.    
        )

        response_message = response.choices[0].message
        
        functions_to_call = []

        if response_message.tool_calls:
            for tool_call in response_message.tool_calls:
                print("도구: ", tool_call)
                name = tool_call.function.name
                args = json.loads(tool_call.function.arguments)
                functions_to_call.append({ "name": name, "args": args })

        return functions_to_call
    ```

    앞의 코드에서는 다음을 수행했습니다.

    - MCP 서버에서 찾아 변환한 함수를 LLM에 전달했습니다.
    - 그런 다음 해당 함수로 LLM을 호출했습니다.
    - 그런 다음 결과를 검사하여 호출해야 할 함수가 있는지 확인합니다.
    - 마지막으로 호출할 함수 배열을 전달합니다.

1. 마지막 단계로 기본 코드를 업데이트해 보겠습니다.

    ```python
    prompt = "2에 20 더하기"

    # LLM에 어떤 도구를 사용할지 물어봅니다.
    functions_to_call = call_llm(prompt, functions)

    # 제안된 함수 호출
    for f in functions_to_call:
        result = await session.call_tool(f["name"], arguments=f["args"])
        print("도구 결과: ", result.content)
    ```

    거기, 마지막 단계였습니다. 위의 코드에서는 다음을 수행합니다.

    - LLM이 프롬프트를 기반으로 호출해야 한다고 생각한 함수를 사용하여 `call_tool`을 통해 MCP 도구를 호출합니다.
    - MCP 서버에 대한 도구 호출 결과를 출력합니다.

</details>

<details>
<summary>.NET</summary>

1. LLM 프롬프트 요청을 수행하는 코드를 보여드리겠습니다.

    ```csharp
    var tools = await GetMcpTools();

    for (int i = 0; i < tools.Count; i++)
    {
        var tool = tools[i];
        Console.WriteLine($"MCP 도구 정의: {i}: {tool}");
    }

    // 0. 채팅 기록 및 사용자 메시지 정의
    var userMessage = "2와 4 더하기";

    chatHistory.Add(new ChatRequestUserMessage(userMessage));

    // 1. 도구 정의
    ChatCompletionsToolDefinition def = CreateToolDefinition();


    // 2. 도구를 포함한 옵션 정의
    var options = new ChatCompletionsOptions(chatHistory)
    {
        Model = "gpt-4o-mini",
        Tools = { tools[0] }
    };

    // 3. 모델 호출  

    ChatCompletions? response = await client.CompleteAsync(options);
    var content = response.Content;

    ```

    앞의 코드에서는 다음을 수행했습니다.

    - MCP 서버에서 도구를 가져왔습니다. `var tools = await GetMcpTools()`.
    - 사용자 프롬프트 `userMessage`를 정의했습니다.
    - 모델 및 도구를 지정하는 옵션 개체를 생성했습니다.
    - LLM에 대한 요청을 했습니다.

1. 마지막 단계로 LLM이 함수를 호출해야 한다고 생각하는지 확인해 보겠습니다.

    ```csharp
    // 4. 응답에 함수 호출이 포함되어 있는지 확인
    ChatCompletionsToolCall? calls = response.ToolCalls.FirstOrDefault();
    for (int i = 0; i < response.ToolCalls.Count; i++)
    {
        var call = response.ToolCalls[i];
        Console.WriteLine($"인수가 있는 도구 호출 {i}: {call.Name} {call.Arguments}");
        //인수가 있는 도구 호출 0: 추가 {"a":2,"b":4}

        var dict = JsonSerializer.Deserialize<Dictionary<string, object>>(call.Arguments);
        var result = await mcpClient.CallToolAsync(
            call.Name,
            dict!,
            cancellationToken: CancellationToken.None
        );

        Console.WriteLine(result.Content.First(c => c.Type == "text").Text);

    }
    ```

    앞의 코드에서는 다음을 수행했습니다.

    - 함수 호출 목록을 반복했습니다.
    - 각 도구 호출에 대해 이름과 인수를 구문 분석하고 MCP 클라이언트를 사용하여 MCP 서버의 도구를 호출합니다. 마지막으로 결과를 출력합니다.

전체 코드는 다음과 같습니다.

```csharp
using Azure;
using Azure.AI.Inference;
using Azure.Identity;
using System.Text.Json;
using ModelContextProtocol.Client;
using ModelContextProtocol.Protocol.Transport;
using System.Text.Json;

var endpoint = "https://models.inference.ai.azure.com";
var token = Environment.GetEnvironmentVariable("GITHUB_TOKEN"); // GitHub 액세스 토큰
var client = new ChatCompletionsClient(new Uri(endpoint), new AzureKeyCredential(token));
var chatHistory = new List<ChatRequestMessage>
{
    new ChatRequestSystemMessage("당신은 AI에 대해 아는 도움이 되는 조수입니다")
};

var clientTransport = new StdioClientTransport(new()
{
    Name = "데모 서버",
    Command = "/workspaces/mcp-for-beginners/03-GettingStarted/02-client/solution/server/bin/Debug/net8.0/server",
    Arguments = [],
});

Console.WriteLine("stdio 전송 설정 중");

await using var mcpClient = await McpClientFactory.CreateAsync(clientTransport);

ChatCompletionsToolDefinition ConvertFrom(string name, string description, JsonElement jsonElement)
{ 
    // 도구를 함수 정의로 변환
    FunctionDefinition functionDefinition = new FunctionDefinition(name)
    {
        Description = description,
        Parameters = BinaryData.FromObjectAsJson(new
        {
            Type = "object",
            Properties = jsonElement
        },
        new JsonSerializerOptions() { PropertyNamingPolicy = JsonNamingPolicy.CamelCase })
    };

    // 도구 정의 만들기
    ChatCompletionsToolDefinition toolDefinition = new ChatCompletionsToolDefinition(functionDefinition);
    return toolDefinition;
}



async Task<List<ChatCompletionsToolDefinition>> GetMcpTools()
{
    Console.WriteLine("도구 나열");
    var tools = await mcpClient.ListToolsAsync();

    List<ChatCompletionsToolDefinition> toolDefinitions = new List<ChatCompletionsToolDefinition>();

    foreach (var tool in tools)
    {
        Console.WriteLine($"도구가 있는 서버에 연결됨: {tool.Name}");
        Console.WriteLine($"도구 설명: {tool.Description}");
        Console.WriteLine($"도구 매개변수: {tool.JsonSchema}");

        JsonElement propertiesElement;
        tool.JsonSchema.TryGetProperty("properties", out propertiesElement);

        var def = ConvertFrom(tool.Name, tool.Description, propertiesElement);
        Console.WriteLine($"도구 정의: {def}");
        toolDefinitions.Add(def);

        Console.WriteLine($"속성: {propertiesElement}");        
    }

    return toolDefinitions;
}

// 1. mcp 서버의 도구 나열

var tools = await GetMcpTools();
for (int i = 0; i < tools.Count; i++)
{
    var tool = tools[i];
    Console.WriteLine($"MCP 도구 정의: {i}: {tool}");
}

// 2. 채팅 기록 및 사용자 메시지 정의
var userMessage = "2와 4 더하기";

chatHistory.Add(new ChatRequestUserMessage(userMessage));


// 3. 도구를 포함한 옵션 정의
var options = new ChatCompletionsOptions(chatHistory)
{
    Model = "gpt-4o-mini",
    Tools = { tools[0] }
};

// 4. 모델 호출  

ChatCompletions? response = await client.CompleteAsync(options);
var content = response.Content;

// 5. 응답에 함수 호출이 포함되어 있는지 확인
ChatCompletionsToolCall? calls = response.ToolCalls.FirstOrDefault();
for (int i = 0; i < response.ToolCalls.Count; i++)
{
    var call = response.ToolCalls[i];
    Console.WriteLine($"인수가 있는 도구 호출 {i}: {call.Name} {call.Arguments}");
    //인수가 있는 도구 호출 0: 추가 {"a":2,"b":4}

    var dict = JsonSerializer.Deserialize<Dictionary<string, object>>(call.Arguments);
    var result = await mcpClient.CallToolAsync(
        call.Name,
        dict!,
        cancellationToken: CancellationToken.None
    );

    Console.WriteLine(result.Content.First(c => c.Type == "text").Text);

}

// 5. 일반 응답 출력
Console.WriteLine($"조수 응답: {content}");
```

</details>

<details>
<summary>Java</summary>

```java
try {
    // MCP 도구를 자동으로 사용하는 자연어 요청 실행
    String response = bot.chat("계산기 서비스를 사용하여 24.5와 17.3의 합계를 계산합니다.");
    System.out.println(response);

    response = bot.chat("144의 제곱근은 얼마입니까?");
    System.out.println(response);

    response = bot.chat("계산기 서비스에 대한 도움말을 보여주세요.");
    System.out.println(response);
} finally {
    mcpClient.close();
}
```

앞의 코드에서는 다음을 수행했습니다.

- MCP 서버 도구와 상호 작용하기 위해 간단한 자연어 프롬프트를 사용했습니다.
- LangChain4j 프레임워크는 다음을 자동으로 처리합니다.
  - 필요할 때 사용자 프롬프트를 도구 호출로 변환
  - LLM의 결정에 따라 적절한 MCP 도구 호출
  - LLM과 MCP 서버 간의 대화 흐름 관리
- `bot.chat()` 메서드는 MCP 도구 실행 결과를 포함할 수 있는 자연어 응답을 반환합니다.
- 이 접근 방식은 사용자가 기본 MCP 구현에 대해 알 필요가 없는 원활한 사용자 경험을 제공합니다.

전체 코드 예제:

```java
public class LangChain4jClient {
    
    public static void main(String[] args) throws Exception {        ChatLanguageModel model = OpenAiOfficialChatModel.builder()
                .isGitHubModels(true)
                .apiKey(System.getenv("GITHUB_TOKEN"))
                .timeout(Duration.ofSeconds(60))
                .modelName("gpt-4.1-nano")
                .timeout(Duration.ofSeconds(60))
                .build();

        McpTransport transport = new HttpMcpTransport.Builder()
                .sseUrl("http://localhost:8080/sse")
                .timeout(Duration.ofSeconds(60))
                .logRequests(true)
                .logResponses(true)
                .build();

        McpClient mcpClient = new DefaultMcpClient.Builder()
                .transport(transport)
                .build();

        ToolProvider toolProvider = McpToolProvider.builder()
                .mcpClients(List.of(mcpClient))
                .build();

        Bot bot = AiServices.builder(Bot.class)
                .chatLanguageModel(model)
                .toolProvider(toolProvider)
                .build();

        try {
            String response = bot.chat("계산기 서비스를 사용하여 24.5와 17.3의 합계를 계산합니다.");
            System.out.println(response);

            response = bot.chat("144의 제곱근은 얼마입니까?");
            System.out.println(response);

            response = bot.chat("계산기 서비스에 대한 도움말을 보여주세요.");
            System.out.println(response);
        } finally {
            mcpClient.close();
        }
    }
}
```

</details>

좋습니다. 해냈습니다!

## 과제

연습에서 코드를 가져와 서버를 더 많은 도구로 구축하십시오. 그런 다음 연습에서와 같이 LLM으로 클라이언트를 만들고 다른 프롬프트로 테스트하여 모든 서버 도구가 동적으로 호출되는지 확인하십시오. 이러한 방식으로 클라이언트를 구축하면 최종 사용자가 정확한 클라이언트 명령 대신 프롬프트를 사용할 수 있고 호출되는 MCP 서버를 인식하지 못하므로 훌륭한 사용자 경험을 얻을 수 있습니다.

## 해결책

[해결책](/03-GettingStarted/03-llm-client/solution/README.md)

## 주요 내용

- 클라이언트에 LLM을 추가하면 사용자가 MCP 서버와 상호 작용하는 더 나은 방법을 제공합니다.
- MCP 서버 응답을 LLM이 이해할 수 있는 것으로 변환해야 합니다.

## 샘플

- [Java 계산기](../samples/java/calculator/README.md)
- [.Net 계산기](../samples/csharp/)
- [JavaScript 계산기](../samples/javascript/README.md)
- [TypeScript 계산기](../samples/typescript/README.md)
- [Python 계산기](../samples/python/)

## 추가 리소스

## 다음 내용

- 다음: [Visual Studio Code를 사용하여 서버 사용](../04-vscode/README.md)