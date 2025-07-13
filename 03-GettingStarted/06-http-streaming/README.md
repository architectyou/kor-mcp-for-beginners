# 모델 컨텍스트 프로토콜(MCP)을 사용한 HTTPS 스트리밍

이 장에서는 HTTPS를 사용하여 모델 컨텍스트 프로토콜(MCP)을 통한 안전하고 확장 가능하며 실시간 스트리밍을 구현하기 위한 포괄적인 가이드를 제공합니다. 스트리밍 동기, 사용 가능한 전송 메커니즘, MCP에서 스트리밍 가능한 HTTP를 구현하는 방법, 보안 모범 사례, SSE에서 마이그레이션하는 방법, 자체 스트리밍 MCP 애플리케이션을 구축하기 위한 실용적인 지침을 다룹니다.

## 전송 메커니즘 및 MCP의 스트리밍

이 섹션에서는 MCP에서 사용 가능한 다양한 전송 메커니즘과 클라이언트와 서버 간의 실시간 통신을 위한 스트리밍 기능을 활성화하는 역할을 살펴봅니다.

### 전송 메커니즘이란?

전송 메커니즘은 클라이언트와 서버 간에 데이터가 교환되는 방식을 정의합니다. MCP는 다양한 환경 및 요구 사항에 맞게 여러 전송 유형을 지원합니다.

- **stdio**: 표준 입력/출력으로, 로컬 및 CLI 기반 도구에 적합합니다. 간단하지만 웹 또는 클라우드에는 적합하지 않습니다.
- **SSE(서버 전송 이벤트)**: 서버가 HTTP를 통해 클라이언트에 실시간 업데이트를 푸시할 수 있도록 합니다. 웹 UI에 적합하지만 확장성 및 유연성이 제한됩니다.
- **스트리밍 가능한 HTTP**: 알림 및 더 나은 확장성을 지원하는 최신 HTTP 기반 스트리밍 전송입니다. 대부분의 프로덕션 및 클라우드 시나리오에 권장됩니다.

### 비교표

이러한 전송 메커니즘 간의 차이점을 이해하려면 아래 비교표를 참조하십시오.

| 전송 | 실시간 업데이트 | 스트리밍 | 확장성 | 사용 사례 |
|---|---|---|---|---|
| stdio | 아니요 | 아니요 | 낮음 | 로컬 CLI 도구 |
| SSE | 예 | 예 | 중간 | 웹, 실시간 업데이트 |
| 스트리밍 가능한 HTTP | 예 | 예 | 높음 | 클라우드, 다중 클라이언트 |

> **팁:** 올바른 전송을 선택하는 것은 성능, 확장성 및 사용자 경험에 영향을 미칩니다. 최신, 확장 가능 및 클라우드 준비 애플리케이션에는 **스트리밍 가능한 HTTP**가 권장됩니다.

이전 장에서 보여준 stdio 및 SSE 전송과 스트리밍 가능한 HTTP가 이 장에서 다루는 전송임을 참고하십시오.

## 스트리밍: 개념 및 동기

효과적인 실시간 통신 시스템을 구현하려면 스트리밍의 기본 개념과 동기를 이해하는 것이 필수적입니다.

**스트리밍**은 전체 응답이 준비될 때까지 기다리지 않고 데이터를 작고 관리하기 쉬운 청크로 또는 일련의 이벤트로 보내고 받을 수 있도록 하는 네트워크 프로그래밍 기술입니다. 이는 특히 다음에 유용합니다.

- 대용량 파일 또는 데이터 세트.
- 실시간 업데이트(예: 채팅, 진행률 표시줄).
- 사용자에게 정보를 계속 제공하려는 장기 실행 계산.

다음은 스트리밍에 대해 높은 수준에서 알아야 할 사항입니다.

- 데이터는 한 번에 모두 전달되는 것이 아니라 점진적으로 전달됩니다.
- 클라이언트는 데이터가 도착하는 대로 처리할 수 있습니다.
- 인지된 대기 시간을 줄이고 사용자 경험을 향상시킵니다.

### 스트리밍을 사용하는 이유

스트리밍을 사용하는 이유는 다음과 같습니다.

- 사용자는 끝에서만 피드백을 받는 것이 아니라 즉시 피드백을 받습니다.
- 실시간 애플리케이션 및 반응형 UI를 가능하게 합니다.
- 네트워크 및 컴퓨팅 리소스를 더 효율적으로 사용합니다.

### 간단한 예: HTTP 스트리밍 서버 및 클라이언트

다음은 스트리밍을 구현하는 간단한 예입니다.

<details>
<summary>Python</summary>

**서버(Python, FastAPI 및 StreamingResponse 사용):**
<details>
<summary>Python</summary>

```python
from fastapi import FastAPI
from fastapi.responses import StreamingResponse
import time

app = FastAPI()

async def event_stream():
    for i in range(1, 6):
        yield f"data: Message {i}\n\n"
        time.sleep(1)

@app.get("/stream")
def stream():
    return StreamingResponse(event_stream(), media_type="text/event-stream")
```

</details>

**클라이언트(Python, requests 사용):**
<details>
<summary>Python</summary>

```python
import requests

with requests.get("http://localhost:8000/stream", stream=True) as r:
    for line in r.iter_lines():
        if line:
            print(line.decode())
```

</details>

이 예제는 서버가 모든 메시지가 준비될 때까지 기다리지 않고 메시지가 사용 가능해지는 대로 클라이언트에 일련의 메시지를 보내는 방법을 보여줍니다.

**작동 방식:**
- 서버는 각 메시지가 준비되는 대로 생성합니다.
- 클라이언트는 각 청크가 도착하는 대로 수신하고 인쇄합니다.

**요구 사항:**
- 서버는 스트리밍 응답을 사용해야 합니다(예: FastAPI의 `StreamingResponse`).
- 클라이언트는 응답을 스트림으로 처리해야 합니다(`requests`에서 `stream=True`).
- Content-Type은 일반적으로 `text/event-stream` 또는 `application/octet-stream`입니다.

</details>

<details>
<summary>Java</summary>

**서버(Java, Spring Boot 및 서버 전송 이벤트 사용):**

```java
@RestController
public class CalculatorController {

    @GetMapping(value = "/calculate", produces = MediaType.TEXT_EVENT_STREAM_VALUE)
    public Flux<ServerSentEvent<String>> calculate(@RequestParam double a,
                                                   @RequestParam double b,
                                                   @RequestParam String op) {
        
        double result;
        switch (op) {
            case "add": result = a + b; break;
            case "sub": result = a - b; break;
            case "mul": result = a * b; break;
            case "div": result = b != 0 ? a / b : Double.NaN; break;
            default: result = Double.NaN;
        }

        return Flux.<ServerSentEvent<String>>just(
                    ServerSentEvent.<String>builder()
                        .event("info")
                        .data("계산 중: " + a + " " + op + " " + b)
                        .build(),
                    ServerSentEvent.<String>builder()
                        .event("result")
                        .data(String.valueOf(result))
                        .build()
                )
                .delayElements(Duration.ofSeconds(1));
    }
}
```

**클라이언트(Java, Spring WebFlux WebClient 사용):**

```java
@SpringBootApplication
public class CalculatorClientApplication implements CommandLineRunner {

    private final WebClient client = WebClient.builder()
            .baseUrl("http://localhost:8080")
            .build();

    @Override
    public void run(String... args) {
        client.get()
                .uri(uriBuilder -> uriBuilder
                        .path("/calculate")
                        .queryParam("a", 7)
                        .queryParam("b", 5)
                        .queryParam("op", "mul")
                        .build())
                .accept(MediaType.TEXT_EVENT_STREAM)
                .retrieve()
                .bodyToFlux(String.class)
                .doOnNext(System.out::println)
                .blockLast();
    }
}
```

**Java 구현 참고 사항:**
- 스트리밍을 위해 `Flux`와 함께 Spring Boot의 반응형 스택 사용
- `ServerSentEvent`는 이벤트 유형으로 구조화된 이벤트 스트리밍 제공
- `WebClient`와 `bodyToFlux()`는 반응형 스트리밍 소비 가능
- `delayElements()`는 이벤트 간 처리 시간 시뮬레이션
- 이벤트는 더 나은 클라이언트 처리를 위해 유형(`info`, `result`)을 가질 수 있음

</details>

### 비교: 클래식 스트리밍 vs MCP 스트리밍

스트리밍이 "클래식" 방식으로 작동하는 방식과 MCP에서 작동하는 방식의 차이점은 다음과 같이 묘사할 수 있습니다.

| 기능 | 클래식 HTTP 스트리밍 | MCP 스트리밍(알림) |
|---|---|---|
| 주 응답 | 청크 | 단일, 끝에서 |
| 진행률 업데이트 | 데이터 청크로 전송 | 알림으로 전송 |
| 클라이언트 요구 사항 | 스트림 처리 필요 | 메시지 처리기 구현 필요 |
| 사용 사례 | 대용량 파일, AI 토큰 스트림 | 진행률, 로그, 실시간 피드백 |

### 관찰된 주요 차이점

또한 몇 가지 주요 차이점은 다음과 같습니다.

- **통신 패턴:**
   - 클래식 HTTP 스트리밍: 데이터를 청크로 보내기 위해 간단한 청크 전송 인코딩 사용
   - MCP 스트리밍: JSON-RPC 프로토콜과 함께 구조화된 알림 시스템 사용

- **메시지 형식:**
   - 클래식 HTTP: 줄 바꿈이 있는 일반 텍스트 청크
   - MCP: 메타데이터가 있는 구조화된 LoggingMessageNotification 개체

- **클라이언트 구현:**
   - 클래식 HTTP: 스트리밍 응답을 처리하는 간단한 클라이언트
   - MCP: 다양한 유형의 메시지를 처리하기 위한 메시지 처리기가 있는 더 정교한 클라이언트

- **진행률 업데이트:**
   - 클래식 HTTP: 진행률은 주 응답 스트림의 일부입니다.
   - MCP: 주 응답이 끝에서 오는 동안 별도의 알림 메시지를 통해 진행률이 전송됩니다.

### 권장 사항

클래식 스트리밍(위에서 `/stream`을 사용하여 보여준 엔드포인트)을 구현할지 MCP를 통한 스트리밍을 선택할지 선택할 때 몇 가지 권장 사항이 있습니다.

- **간단한 스트리밍 요구 사항의 경우:** 클래식 HTTP 스트리밍은 구현하기 더 간단하며 기본적인 스트리밍 요구 사항에 충분합니다.

- **복잡하고 대화형 애플리케이션의 경우:** MCP 스트리밍은 더 풍부한 메타데이터와 알림 및 최종 결과 간의 분리를 통해 더 구조화된 접근 방식을 제공합니다.

- **AI 애플리케이션의 경우:** MCP의 알림 시스템은 사용자에게 진행 상황을 계속 알려야 하는 장기 실행 AI 작업에 특히 유용합니다.

## MCP의 스트리밍

좋습니다. 지금까지 클래식 스트리밍과 MCP의 스트리밍 간의 차이점에 대한 몇 가지 권장 사항과 비교를 보았습니다. MCP에서 스트리밍을 정확히 어떻게 활용할 수 있는지 자세히 살펴보겠습니다.

MCP 프레임워크 내에서 스트리밍이 작동하는 방식을 이해하는 것은 장기 실행 작업 중에 사용자에게 실시간 피드백을 제공하는 반응형 애플리케이션을 구축하는 데 필수적입니다.

MCP에서 스트리밍은 주 응답을 청크로 보내는 것이 아니라 도구가 요청을 처리하는 동안 클라이언트에 **알림**을 보내는 것입니다. 이러한 알림에는 진행률 업데이트, 로그 또는 기타 이벤트가 포함될 수 있습니다.

### 작동 방식

주 결과는 여전히 단일 응답으로 전송됩니다. 그러나 알림은 처리 중에 별도의 메시지로 전송되어 클라이언트를 실시간으로 업데이트할 수 있습니다. 클라이언트는 이러한 알림을 처리하고 표시할 수 있어야 합니다.

## 알림이란?

"알림"이라고 했는데, MCP의 맥락에서 그것은 무엇을 의미합니까?

알림은 장기 실행 작업 중에 진행 상황, 상태 또는 기타 이벤트에 대해 알리기 위해 서버에서 클라이언트로 전송되는 메시지입니다. 알림은 투명성과 사용자 경험을 향상시킵니다.

예를 들어, 클라이언트는 서버와의 초기 핸드셰이크가 완료되면 알림을 보내야 합니다.

알림은 JSON 메시지로 다음과 같습니다.

```json
{
  jsonrpc: "2.0";
  method: string;
  params?: {
    [key: string]: unknown;
  };
}
```

알림은 MCP에서 ["로깅"](https://modelcontextprotocol.io/specification/draft/server/utilities/logging)이라는 주제에 속합니다.

로깅이 작동하려면 서버가 다음과 같이 기능/기능으로 활성화해야 합니다.

```json
{
  "capabilities": {
    "logging": {}
  }
}
```

> [!NOTE]
> 사용되는 SDK에 따라 로깅이 기본적으로 활성화되거나 서버 구성에서 명시적으로 활성화해야 할 수 있습니다.


다양한 유형의 알림이 있습니다.

| 수준 | 설명 | 예제 사용 사례 |
|---|---|---|
| debug | 자세한 디버깅 정보 | 함수 진입/종료 지점 |
| info | 일반 정보 메시지 | 작업 진행률 업데이트 |
| notice | 정상적이지만 중요한 이벤트 | 구성 변경 |
| warning | 경고 조건 | 사용되지 않는 기능 사용 |
| error | 오류 조건 | 작업 실패 |
| critical | 심각한 조건 | 시스템 구성 요소 실패 |
| alert | 즉시 조치해야 함 | 데이터 손상 감지 |
| emergency | 시스템을 사용할 수 없음 | 전체 시스템 실패 |


## MCP에서 알림 구현

MCP에서 알림을 구현하려면 실시간 업데이트를 처리하도록 서버 및 클라이언트 측을 모두 설정해야 합니다. 이를 통해 애플리케이션은 장기 실행 작업 중에 사용자에게 즉각적인 피드백을 제공할 수 있습니다.

### 서버 측: 알림 보내기

서버 측부터 시작하겠습니다. MCP에서는 요청을 처리하는 동안 알림을 보낼 수 있는 도구를 정의합니다. 서버는 컨텍스트 개체(일반적으로 `ctx`)를 사용하여 클라이언트에 메시지를 보냅니다.

<details>
<summary>Python</summary>

<details>
<summary>Python</summary>

```python
@mcp.tool(description="진행률 알림을 보내는 도구")
async def process_files(message: str, ctx: Context) -> TextContent:
    await ctx.info("파일 1/3 처리 중...")
    await ctx.info("파일 2/3 처리 중...")
    await ctx.info("파일 3/3 처리 중...")
    return TextContent(type="text", text=f"완료: {message}")
```

앞의 예제에서 `process_files` 도구는 각 파일을 처리하는 동안 클라이언트에 세 개의 알림을 보냅니다. `ctx.info()` 메서드는 정보 메시지를 보내는 데 사용됩니다.

</details>

또한 알림을 활성화하려면 서버가 스트리밍 전송(예: `streamable-http`)을 사용하고 클라이언트가 알림을 처리하기 위한 메시지 처리기를 구현하는지 확인하십시오. `streamable-http` 전송을 사용하도록 서버를 설정하는 방법은 다음과 같습니다.

```python
mcp.run(transport="streamable-http")
```

</details>

<details>
<summary>.NET</summary>

```csharp
[Tool("진행률 알림을 보내는 도구")]
public async Task<TextContent> ProcessFiles(string message, ToolContext ctx)
{
    await ctx.Info("파일 1/3 처리 중...");
    await ctx.Info("파일 2/3 처리 중...");
    await ctx.Info("파일 3/3 처리 중...");
    return new TextContent
    {
        Type = "text",
        Text = $"완료: {message}"
    };
}
```

이 .NET 예제에서 `ProcessFiles` 도구는 `Tool` 특성으로 데코레이터되어 각 파일을 처리하는 동안 클라이언트에 세 개의 알림을 보냅니다. `ctx.Info()` 메서드는 정보 메시지를 보내는 데 사용됩니다.

.NET MCP 서버에서 알림을 활성화하려면 스트리밍 전송을 사용하고 있는지 확인하십시오.

```csharp
var builder = McpBuilder.Create();
await builder
    .UseStreamableHttp() // 스트리밍 가능한 HTTP 전송 활성화
    .Build()
    .RunAsync();
```

</details>

### 클라이언트 측: 알림 수신

클라이언트는 메시지 처리기를 구현하여 도착하는 대로 알림을 처리하고 표시해야 합니다.

<details>
<summary>Python</summary>

```python
async def message_handler(message):
    if isinstance(message, types.ServerNotification):
        print("알림: ", message)
    else:
        print("서버 메시지: ", message)

async with ClientSession(
   read_stream, 
   write_stream,
   logging_callback=logging_collector,
   message_handler=message_handler,
) as session:
```

앞의 코드에서 `message_handler` 함수는 들어오는 메시지가 알림인지 확인합니다. 알림이면 알림을 인쇄하고, 그렇지 않으면 일반 서버 메시지로 처리합니다. 또한 `ClientSession`이 들어오는 알림을 처리하기 위해 `message_handler`로 초기화되는 방법을 확인하십시오.

</details>

<details>
<summary>.NET</summary>

```csharp
// 메시지 처리기 정의
void MessageHandler(IJsonRpcMessage message)
{
    if (message is ServerNotification notification)
    {
        Console.WriteLine($"알림: {notification}");
    }
    else
    {
        Console.WriteLine($"서버 메시지: {message}");
    }
}

// 메시지 처리기와 함께 클라이언트 세션 만들기 및 사용
var clientOptions = new ClientSessionOptions
{
    MessageHandler = MessageHandler,
    LoggingCallback = (level, message) => Console.WriteLine($"[{level}] {message}")
};

using var client = new ClientSession(readStream, writeStream, clientOptions);
await client.InitializeAsync();

// 이제 클라이언트는 MessageHandler를 통해 알림을 처리합니다.
```

이 .NET 예제에서 `MessageHandler` 함수는 들어오는 메시지가 알림인지 확인합니다. 알림이면 알림을 인쇄하고, 그렇지 않으면 일반 서버 메시지로 처리합니다. `ClientSession`은 `ClientSessionOptions`를 통해 메시지 처리기로 초기화됩니다.

</details>

알림을 활성화하려면 서버가 스트리밍 전송(예: `streamable-http`)을 사용하고 클라이언트가 알림을 처리하기 위한 메시지 처리기를 구현하는지 확인하십시오.

## 진행률 알림 및 시나리오

이 섹션에서는 MCP의 진행률 알림 개념, 중요성, 스트리밍 가능한 HTTP를 사용하여 구현하는 방법을 설명합니다. 또한 이해를 강화하기 위한 실용적인 과제도 포함되어 있습니다.

진행률 알림은 장기 실행 작업 중에 서버에서 클라이언트로 전송되는 실시간 메시지입니다. 전체 프로세스가 완료될 때까지 기다리지 않고 서버는 처리된 각 항목에 대한 알림을 보냅니다. 이는 투명성, 사용자 경험 및 디버깅 용이성을 향상시킵니다.

**예:**

```text

"문서 1/10 처리 중"
"문서 2/10 처리 중"
...
"처리 완료!"

```

### 진행률 알림을 사용하는 이유

진행률 알림은 여러 가지 이유로 필수적입니다.

- **더 나은 사용자 경험:** 사용자는 작업이 진행됨에 따라 업데이트를 확인하며, 끝에서만 확인하는 것이 아닙니다.
- **실시간 피드백:** 클라이언트는 진행률 표시줄 또는 로그를 표시하여 앱이 반응하는 것처럼 느끼게 할 수 있습니다.
- **더 쉬운 디버깅 및 모니터링:** 개발자와 사용자는 프로세스가 느리거나 멈출 수 있는 위치를 확인할 수 있습니다.

### 진행률 알림 구현 방법

MCP에서 진행률 알림을 구현하는 방법은 다음과 같습니다.

- **서버에서:** `ctx.info()` 또는 `ctx.log()`를 사용하여 각 항목이 처리될 때 알림을 보냅니다. 그러면 주 결과가 준비되기 전에 클라이언트에 메시지가 전송됩니다.
- **클라이언트에서:** 도착하는 대로 알림을 수신하고 표시하는 메시지 처리기를 구현합니다. 이 처리기는 알림과 최종 결과를 구별합니다.

**서버 예제:**

<details>
<summary>Python</summary>

```python
@mcp.tool(description="진행률 알림을 보내는 도구")
async def process_files(message: str, ctx: Context) -> TextContent:
    for i in range(1, 11):
        await ctx.info(f"문서 {i}/10 처리 중")
    await ctx.info("처리 완료!")
    return TextContent(type="text", text=f"완료: {message}")
```

</details>

**클라이언트 예제:**

<details>
<summary>Python</summary>

```python
async def message_handler(message):
    if isinstance(message, types.ServerNotification):
        print("알림: ", message)
    else:
        print("서버 메시지: ", message)
```

</details>

## 보안 고려 사항

HTTP 기반 전송을 사용하여 MCP 서버를 구현할 때 보안은 여러 공격 벡터 및 보호 메커니즘에 대한 신중한 주의가 필요한 가장 중요한 관심사가 됩니다.

### 개요

HTTP를 통해 MCP 서버를 노출할 때 보안은 중요합니다. 스트리밍 가능한 HTTP는 새로운 공격 표면을 도입하며 신중한 구성이 필요합니다.

### 주요 사항
- **Origin 헤더 유효성 검사**: DNS 재바인딩 공격을 방지하기 위해 항상 `Origin` 헤더의 유효성을 검사합니다.
- **Localhost 바인딩**: 로컬 개발의 경우 서버를 `localhost`에 바인딩하여 공용 인터넷에 노출되지 않도록 합니다.
- **인증**: 프로덕션 배포를 위해 인증(예: API 키, OAuth)을 구현합니다.
- **CORS**: 액세스를 제한하도록 CORS(교차 출처 리소스 공유) 정책을 구성합니다.
- **HTTPS**: 트래픽을 암호화하기 위해 프로덕션에서 HTTPS를 사용합니다.

### 모범 사례
- 유효성 검사 없이 들어오는 요청을 절대 신뢰하지 마십시오.
- 모든 액세스 및 오류를 기록하고 모니터링하십시오.
- 보안 취약점을 패치하기 위해 종속성을 정기적으로 업데이트하십시오.

### 과제
- 개발 용이성과 보안 균형 맞추기
- 다양한 클라이언트 환경과의 호환성 보장

## SSE에서 스트리밍 가능한 HTTP로 업그레이드

현재 서버 전송 이벤트(SSE)를 사용하는 애플리케이션의 경우 스트리밍 가능한 HTTP로 마이그레이션하면 MCP 구현에 대한 향상된 기능과 더 나은 장기적인 지속 가능성을 제공합니다.

### 업그레이드하는 이유

SSE에서 스트리밍 가능한 HTTP로 업그레이드해야 하는 두 가지 설득력 있는 이유는 다음과 같습니다.

- 스트리밍 가능한 HTTP는 SSE보다 더 나은 확장성, 호환성 및 풍부한 알림 지원을 제공합니다.
- 새로운 MCP 애플리케이션에 권장되는 전송입니다.

### 마이그레이션 단계

MCP 애플리케이션에서 SSE에서 스트리밍 가능한 HTTP로 마이그레이션하는 방법은 다음과 같습니다.

- `mcp.run()`에서 `transport="streamable-http"`를 사용하도록 **서버 코드 업데이트**.
- SSE 클라이언트 대신 `streamablehttp_client`를 사용하도록 **클라이언트 코드 업데이트**.
- 알림을 처리하기 위해 클라이언트에 **메시지 처리기 구현**.
- 기존 도구 및 워크플로와의 **호환성 테스트**.

### 호환성 유지

마이그레이션 프로세스 중에 기존 SSE 클라이언트와의 호환성을 유지하는 것이 좋습니다. 몇 가지 전략은 다음과 같습니다.

- 다른 엔드포인트에서 두 전송을 모두 실행하여 SSE 및 스트리밍 가능한 HTTP를 모두 지원할 수 있습니다.
- 클라이언트를 새 전송으로 점진적으로 마이그레이션합니다.

### 과제

마이그레이션 중에 다음 과제를 해결해야 합니다.

- 모든 클라이언트가 업데이트되었는지 확인
- 알림 전달의 차이점 처리

## 보안 고려 사항

HTTP 기반 전송과 같은 스트리밍 가능한 HTTP를 MCP에서 구현할 때 보안은 항상 최우선 순위가 되어야 합니다.

HTTP 기반 전송을 사용하여 MCP 서버를 구현할 때 보안은 여러 공격 벡터 및 보호 메커니즘에 대한 신중한 주의가 필요한 가장 중요한 관심사가 됩니다.

### 개요

HTTP를 통해 MCP 서버를 노출할 때 보안은 중요합니다. 스트리밍 가능한 HTTP는 새로운 공격 표면을 도입하며 신중한 구성이 필요합니다.

다음은 몇 가지 주요 보안 고려 사항입니다.

- **Origin 헤더 유효성 검사**: DNS 재바인딩 공격을 방지하기 위해 항상 `Origin` 헤더의 유효성을 검사합니다.
- **Localhost 바인딩**: 로컬 개발의 경우 서버를 `localhost`에 바인딩하여 공용 인터넷에 노출되지 않도록 합니다.
- **인증**: 프로덕션 배포를 위해 인증(예: API 키, OAuth)을 구현합니다.
- **CORS**: 액세스를 제한하도록 CORS(교차 출처 리소스 공유) 정책을 구성합니다.
- **HTTPS**: 트래픽을 암호화하기 위해 프로덕션에서 HTTPS를 사용합니다.

### 모범 사례

또한 MCP 스트리밍 서버에서 보안을 구현할 때 따라야 할 몇 가지 모범 사례는 다음과 같습니다.

- 유효성 검사 없이 들어오는 요청을 절대 신뢰하지 마십시오.
- 모든 액세스 및 오류를 기록하고 모니터링하십시오.
- 보안 취약점을 패치하기 위해 종속성을 정기적으로 업데이트하십시오.

### 과제

MCP 스트리밍 서버에서 보안을 구현할 때 몇 가지 과제에 직면하게 됩니다.

- 개발 용이성과 보안 균형 맞추기
- 다양한 클라이언트 환경과의 호환성 보장

### 과제: 자체 스트리밍 MCP 앱 구축

**시나리오:**
서버가 항목 목록(예: 파일 또는 문서)을 처리하고 처리된 각 항목에 대한 알림을 보내는 MCP 서버 및 클라이언트를 구축합니다. 클라이언트는 도착하는 대로 각 알림을 표시해야 합니다.

**단계:**

1. 목록을 처리하고 각 항목에 대한 알림을 보내는 서버 도구를 구현합니다.
2. 실시간으로 알림을 표시하기 위한 메시지 처리기가 있는 클라이언트를 구현합니다.
3. 서버와 클라이언트를 모두 실행하여 구현을 테스트하고 알림을 관찰합니다.

[해결책](./solution/README.md)

## 추가 자료 및 다음 단계

MCP 스트리밍 여정을 계속하고 지식을 확장하려면 이 섹션에서 더 고급 애플리케이션을 구축하기 위한 추가 리소스와 제안된 다음 단계를 제공합니다.

### 추가 자료

- [Microsoft: HTTP 스트리밍 소개](https://learn.microsoft.com/aspnet/core/fundamentals/http-requests?view=aspnetcore-8.0&WT.mc_id=%3Fwt.mc_id%3DMVP_452430#streaming)
- [Microsoft: 서버 전송 이벤트(SSE)](https://learn.microsoft.com/azure/application-gateway/for-containers/server-sent-events?tabs=server-sent-events-gateway-api&WT.mc_id=%3Fwt.mc_id%3DMVP_452430)
- [Microsoft: ASP.NET Core의 CORS](https://learn.microsoft.com/en-us/aspnet/core/security/cors?view=aspnetcore-8.0&WT.mc_id=%3Fwt.mc_id%3DMVP_452430)
- [Python requests: 스트리밍 요청](https://requests.readthedocs.io/en/latest/user/advanced/#streaming-requests)

### 다음 단계

- 실시간 분석, 채팅 또는 공동 편집을 위해 스트리밍을 사용하는 더 고급 MCP 도구를 구축해 보십시오.
- 라이브 UI 업데이트를 위해 프런트엔드 프레임워크(React, Vue 등)와 MCP 스트리밍 통합을 탐색해 보십시오.
- 다음: [VSCode용 AI Toolkit 활용](../07-aitk/README.md)