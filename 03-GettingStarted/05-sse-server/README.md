# SSE 서버

SSE(Server Sent Events)는 서버-클라이언트 스트리밍을 위한 표준으로, 서버가 HTTP를 통해 클라이언트에 실시간 업데이트를 푸시할 수 있도록 합니다. 이는 채팅 애플리케이션, 알림 또는 실시간 데이터 피드와 같이 실시간 업데이트가 필요한 애플리케이션에 특히 유용합니다. 또한 서버는 예를 들어 클라우드 어딘가에서 실행될 수 있는 서버에 상주하므로 여러 클라이언트가 동시에 사용할 수 있습니다.

## 개요

이 단원에서는 SSE 서버를 구축하고 사용하는 방법을 다룹니다.

## 학습 목표

이 단원을 마치면 다음을 수행할 수 있습니다.

- SSE 서버 구축
- Inspector를 사용하여 SSE 서버 디버깅
- Visual Studio Code를 사용하여 SSE 서버 사용


## SSE, 작동 방식

SSE는 지원되는 두 가지 전송 유형 중 하나입니다. 이전 단원에서 첫 번째 stdio를 사용하는 것을 이미 보았습니다. 차이점은 다음과 같습니다.

- SSE는 연결과 메시지라는 두 가지를 처리해야 합니다.
- 이는 어디든 상주할 수 있는 서버이므로 Inspector 및 Visual Studio Code와 같은 도구를 사용하는 방식에 반영해야 합니다. 즉, 서버를 시작하는 방법을 가리키는 대신 연결을 설정할 수 있는 엔드포인트를 가리킵니다. 아래 예제 코드를 참조하십시오.


    <details>
    <summary>TypeScript</summary>

    ```typescript
    app.get("/sse", async (_: Request, res: Response) => {
        const transport = new SSEServerTransport('/messages', res);
        transports[transport.sessionId] = transport;
        res.on("close", () => {
            delete transports[transport.sessionId];
        });
        await server.connect(transport);
    });

    app.post("/messages", async (req: Request, res: Response) => {
        const sessionId = req.query.sessionId as string;
        const transport = transports[sessionId];
        if (transport) {
            await transport.handlePostMessage(req, res);
        } else {
            res.status(400).send('sessionId에 대한 전송을 찾을 수 없습니다');
        }
    });
    ```

    앞의 코드에서:

    - `/sse`는 경로로 설정됩니다. 이 경로로 요청이 이루어지면 새 전송 인스턴스가 생성되고 서버는 이 전송을 사용하여 *연결*합니다.
    - `/messages`는 들어오는 메시지를 처리하는 경로입니다.

    </details>

    <details>
    <summary>Python</summary>

    ```python
    mcp = FastMCP("내 앱")

    @mcp.tool()
    def add(a: int, b: int) -> int:
        """두 숫자 더하기"""
        return a + b

    # 기존 ASGI 서버에 SSE 서버 마운트
    app = Starlette(
        routes=[
            Mount('/', app=mcp.sse_app()),
        ]
    )

    ```

    앞의 코드에서:

    - ASGI 서버 인스턴스(특히 Starletter 사용)를 만들고 기본 경로 `/`를 마운트합니다.

      백그라운드에서 `/sse` 및 `/messages` 경로가 연결 및 메시지를 각각 처리하도록 설정됩니다. 도구와 같은 기능을 추가하는 나머지 앱은 stdio 서버와 마찬가지로 작동합니다.

    </details>

    <details>
    <summary>.NET</summary>

    ```csharp
    var builder = WebApplication.CreateBuilder(args);
    builder.Services
        .AddMcpServer()
        .WithTools<Tools>();


    builder.Services.AddHttpClient();

    var app = builder.Build();

    app.MapMcp();
    ```

    웹 서버에서 SSE를 지원하는 웹 서버로 전환하는 데 도움이 되는 두 가지 메서드가 있습니다.

    - `AddMcpServer`, 이 메서드는 기능을 추가합니다.
    - `MapMcp`, 이 메서드는 `/SSE` 및 `/messages`와 같은 경로를 추가합니다.


    </details>

이제 SSE에 대해 조금 더 알았으니 다음으로 SSE 서버를 구축해 보겠습니다.

## 연습: SSE 서버 만들기

서버를 만들려면 두 가지를 염두에 두어야 합니다.

- 연결 및 메시지용 엔드포인트를 노출하려면 웹 서버를 사용해야 합니다.
- stdio를 사용할 때와 마찬가지로 도구, 리소스 및 프롬프트로 서버를 구축합니다.

### -1- 서버 인스턴스 만들기

서버를 만들려면 stdio와 동일한 유형을 사용합니다. 그러나 전송의 경우 SSE를 선택해야 합니다.

<details>
<summary>Typescript</summary>

```typescript
import { Request, Response } from "express";
import express from "express";
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { SSEServerTransport } from "@modelcontextprotocol/sdk/server/sse.js";

const server = new McpServer({
  name: "example-server",
  version: "1.0.0"
});

const app = express();

const transports: {[sessionId: string]: SSEServerTransport} = {};
```

앞의 코드에서는 다음을 수행했습니다.

- 서버 인스턴스를 만들었습니다.
- 웹 프레임워크 express를 사용하여 앱을 정의했습니다.
- 들어오는 연결을 저장하는 데 사용할 transports 변수를 만들었습니다.

</details>

<details>
<summary>Python</summary>

```python
from starlette.applications import Starlette
from starlette.routing import Mount, Host
from mcp.server.fastmcp import FastMCP


mcp = FastMCP("내 앱")
```

앞의 코드에서는 다음을 수행했습니다.

- Starlette(ASGI 프레임워크)와 함께 필요한 라이브러리를 가져왔습니다.
- MCP 서버 인스턴스 `mcp`를 만들었습니다.

</details>

<details>
<summary>.NET</summary>

```csharp
var builder = WebApplication.CreateBuilder(args);
builder.Services
    .AddMcpServer();


builder.Services.AddHttpClient();

var app = builder.Build();

// TODO: 경로 추가 
```

이 시점에서 다음을 수행했습니다.

- 웹 앱을 만들었습니다.
- `AddMcpServer`를 통해 MCP 기능에 대한 지원을 추가했습니다.

</details>

다음으로 필요한 경로를 추가해 보겠습니다.

### -2- 경로 추가

다음으로 연결 및 들어오는 메시지를 처리하는 경로를 추가해 보겠습니다.

<details>
<summary>Typescript</summary>

```typescript
app.get("/sse", async (_: Request, res: Response) => {
  const transport = new SSEServerTransport('/messages', res);
  transports[transport.sessionId] = transport;
  res.on("close", () => {
    delete transports[transport.sessionId];
  });
  await server.connect(transport);
});

app.post("/messages", async (req: Request, res: Response) => {
  const sessionId = req.query.sessionId as string;
  const transport = transports[sessionId];
  if (transport) {
    await transport.handlePostMessage(req, res);
  } else {
    res.status(400).send('sessionId에 대한 전송을 찾을 수 없습니다');
  }
});

app.listen(3001);
```

앞의 코드에서는 다음을 정의했습니다.

- SSE 유형의 전송을 인스턴스화하고 MCP 서버에서 `connect`를 호출하는 `/sse` 경로.
- 들어오는 메시지를 처리하는 `/messages` 경로.

</details>

<details>
<summary>Python</summary>

```python
app = Starlette(
    routes=[
        Mount('/', app=mcp.sse_app()),
    ]
)
```

앞의 코드에서는 다음을 수행했습니다.

- Starlette 프레임워크를 사용하여 ASGI 앱 인스턴스를 만들었습니다. 그 일환으로 `mcp.sse_app()`을 경로 목록에 전달했습니다. 그러면 앱 인스턴스에 `/sse` 및 `/messages` 경로가 마운트됩니다.

</details>

<details>
<summary>.NET</summary>

```csharp
var builder = WebApplication.CreateBuilder(args);
builder.Services
    .AddMcpServer();

builder.Services.AddHttpClient();

var app = builder.Build();

app.MapMcp();
```

마지막에 `add.MapMcp()` 코드 한 줄을 추가했습니다. 이는 이제 `/SSE` 및 `/messages` 경로가 있음을 의미합니다.

</details>


다음으로 서버에 기능을 추가해 보겠습니다.

### -3- 서버 기능 추가

이제 SSE 관련 모든 것이 정의되었으므로 도구, 프롬프트 및 리소스와 같은 서버 기능을 추가해 보겠습니다.

<details>
<summary>Typescript</summary>

```typescript
server.tool("random-joke", "척 노리스 API에서 반환된 농담", {},
  async () => {
    const response = await fetch("https://api.chucknorris.io/jokes/random");
    const data = await response.json();

    return {
      content: [
        {
          type: "text",
          text: data.value
        }
      ]
    };
  }
);
```

예를 들어 도구를 추가하는 방법은 다음과 같습니다. 이 특정 도구는 척 노리스 API를 호출하고 JSON 응답을 반환하는 "random-joke"라는 도구 호출을 만듭니다.

</details>

<details>
<summary>Python</summary>

```python
@mcp.tool()
def add(a: int, b: int) -> int:
    """두 숫자 더하기"""
    return a + b
```

이제 서버에 하나의 도구가 있습니다.

</details>

전체 코드는 다음과 같아야 합니다.

<details>
<summary>Typescript</summary>

```typescript
// server-sse.ts
import { Request, Response } from "express";
import express from "express";
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { SSEServerTransport } from "@modelcontextprotocol/sdk/server/sse.js";

// MCP 서버 만들기
const server = new McpServer({
  name: "example-server",
  version: "1.0.0",
});

const app = express();

const transports: { [sessionId: string]: SSEServerTransport } = {};

app.get("/sse", async (_: Request, res: Response) => {
  const transport = new SSEServerTransport("/messages", res);
  transports[transport.sessionId] = transport;
  res.on("close", () => {
    delete transports[transport.sessionId];
  });
  await server.connect(transport);
});

app.post("/messages", async (req: Request, res: Response) => {
  const sessionId = req.query.sessionId as string;
  const transport = transports[sessionId];
  if (transport) {
    await transport.handlePostMessage(req, res);
  } else {
    res.status(400).send("sessionId에 대한 전송을 찾을 수 없습니다");
  }
});

server.tool("random-joke", "척 노리스 API에서 반환된 농담", {}, async () => {
  const response = await fetch("https://api.chucknorris.io/jokes/random");
  const data = await response.json();

  return {
    content: [
      {
        type: "text",
        text: data.value,
      },
    ],
  };
});

app.listen(3001);
```

</details>

<details>
<summary>Python</summary>

```python
from starlette.applications import Starlette
from starlette.routing import Mount, Host
from mcp.server.fastmcp import FastMCP


mcp = FastMCP("내 앱")

@mcp.tool()
def add(a: int, b: int) -> int:
    """두 숫자 더하기"""
    return a + b

# 기존 ASGI 서버에 SSE 서버 마운트
app = Starlette(
    routes=[
        Mount('/', app=mcp.sse_app()),
    ]
)
```

</details>

<details>
<summary>.NET</summary>

1. 먼저 도구를 만들어 보겠습니다. 이를 위해 *Tools.cs* 파일을 다음 내용으로 만듭니다.

  ```csharp
  using System.ComponentModel;
  using System.Text.Json;
  using ModelContextProtocol.Server;

  namespace server;

  [McpServerToolType]
  public sealed class Tools
  {

      public Tools()
      {
      
      }

      [McpServerTool, Description("두 숫자를 더합니다.")]
      public async Task<string> AddNumbers(
          [Description("첫 번째 숫자")] int a,
          [Description("두 번째 숫자")] int b)
      {
          return (a + b).ToString();
      }

  }
  ```

  여기서 다음을 추가했습니다.

  - `McpServerToolType` 데코레이터가 있는 `Tools` 클래스를 만들었습니다.
  - `McpServerTool`로 메서드를 데코레이터하여 `AddNumbers` 도구를 정의했습니다. 또한 매개변수와 구현을 제공했습니다.

1. 방금 만든 `Tools` 클래스를 활용해 보겠습니다.

  ```csharp
  var builder = WebApplication.CreateBuilder(args);
  builder.Services
      .AddMcpServer()
      .WithTools<Tools>();


  builder.Services.AddHttpClient();

  var app = builder.Build();

  app.MapMcp();
  ```

  `WithTools`에 대한 호출을 추가하여 도구를 포함하는 클래스로 `Tools`를 지정했습니다. 이제 준비되었습니다.


</details>

좋습니다. SSE를 사용하는 서버가 있습니다. 다음으로 실행해 보겠습니다.

## 연습: Inspector로 SSE 서버 디버깅

Inspector는 이전 단원 [첫 번째 서버 만들기](/03-GettingStarted/01-first-server/README.md)에서 본 훌륭한 도구입니다. 여기에서도 Inspector를 사용할 수 있는지 살펴보겠습니다.

### -1- Inspector 실행

Inspector를 실행하려면 먼저 SSE 서버가 실행 중이어야 하므로 다음을 수행해 보겠습니다.

1. 서버 실행

    <details>
    <summary>Typescript</summary>

    ```sh
    tsx && node ./build/server-sse.ts
    ```

    </details>

    <details>
    <summary>Python</summary>

    ```sh
    uvicorn server:app
    ```

    `pip install "mcp[cli]"`를 입력했을 때 설치된 실행 파일 `uvicorn`을 사용하는 방법을 확인하십시오. `server:app`을 입력하면 `server.py` 파일을 실행하고 `app`이라는 Starlette 인스턴스를 갖게 됩니다.
    </details>

    <details>
    <summary>.NET</summary>

    ```sh
    dotnet run
    ```

    이렇게 하면 서버가 시작됩니다. 서버와 상호 작용하려면 새 터미널이 필요합니다.

    </details>

1. Inspector 실행

    > ![NOTE]
    > 서버가 실행 중인 터미널 창과 별도의 터미널 창에서 이 명령을 실행하십시오. 또한 아래 명령을 서버가 실행 중인 URL에 맞게 조정해야 합니다.

    ```sh
    npx @modelcontextprotocol/inspector --cli http://localhost:8000/sse --method tools/list
    ```

    Inspector 실행은 모든 런타임에서 동일하게 보입니다. 서버 경로와 서버 시작 명령을 전달하는 대신 서버가 실행 중인 URL을 전달하고 `/sse` 경로도 지정하는 방법을 확인하십시오.

### -2- 도구 사용해 보기

드롭다운 목록에서 SSE를 선택하고 서버가 실행 중인 URL 필드(예: http:localhost:4321/sse)를 채워 서버에 연결합니다. 이제 "연결" 버튼을 클릭합니다. 이전과 마찬가지로 도구를 나열하고 도구를 선택한 다음 입력 값을 제공합니다. 다음과 같은 결과가 표시되어야 합니다.

![Inspector에서 실행 중인 SSE 서버](./assets/sse-inspector.png)

좋습니다. Inspector와 함께 작업할 수 있습니다. 다음으로 Visual Studio Code와 함께 작업하는 방법을 살펴보겠습니다.

## 과제

더 많은 기능으로 서버를 구축해 보십시오. 예를 들어 API를 호출하는 도구를 추가하려면 [이 페이지](https://api.chucknorris.io/)를 참조하십시오. 서버가 어떻게 보여야 하는지는 직접 결정하십시오. 즐거운 시간 보내십시오 :)

## 해결책

[해결책](./solution/README.md) 다음은 작동하는 코드가 포함된 가능한 해결책입니다.

## 주요 내용

이 장의 주요 내용은 다음과 같습니다.

- SSE는 stdio 다음으로 지원되는 두 번째 전송입니다.
- SSE를 지원하려면 웹 프레임워크를 사용하여 들어오는 연결 및 메시지를 관리해야 합니다.
- stdio 서버와 마찬가지로 Inspector 및 Visual Studio Code를 모두 사용하여 SSE 서버를 사용할 수 있습니다. stdio와 SSE 간에 약간의 차이가 있음을 참고하십시오. SSE의 경우 서버를 별도로 시작한 다음 Inspector 도구를 실행해야 합니다. Inspector 도구의 경우 URL을 지정해야 한다는 점에서도 약간의 차이가 있습니다.

## 샘플

- [Java 계산기](../samples/java/calculator/README.md)
- [.Net 계산기](../samples/csharp/)
- [JavaScript 계산기](../samples/javascript/README.md)
- [TypeScript 계산기](../samples/typescript/README.md)
- [Python 계산기](../samples/python/)

## 추가 리소스

- [SSE](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events)

## 다음 내용

- 다음: [MCP를 사용한 HTTP 스트리밍(스트리밍 가능한 HTTP)](../06-http-streaming/README.md)
