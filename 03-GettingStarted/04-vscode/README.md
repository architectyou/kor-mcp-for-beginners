# GitHub Copilot 에이전트 모드에서 서버 사용

Visual Studio Code와 GitHub Copilot은 클라이언트 역할을 하여 MCP 서버를 사용할 수 있습니다. 왜 그렇게 해야 하는지 궁금할 것입니다. MCP 서버의 모든 기능은 이제 IDE 내에서 사용할 수 있습니다. 예를 들어 GitHub의 MCP 서버를 추가하면 터미널에서 특정 명령을 입력하는 대신 프롬프트를 통해 GitHub를 제어할 수 있습니다. 또는 자연어를 통해 제어되는 개발자 경험을 향상시킬 수 있는 모든 것을 상상해 보십시오. 이제 이점이 보이지 않습니까?

## 개요

이 단원에서는 Visual Studio Code와 GitHub Copilot의 에이전트 모드를 MCP 서버의 클라이언트로 사용하는 방법을 다룹니다.

## 학습 목표

이 단원을 마치면 다음을 수행할 수 있습니다.

- Visual Studio Code를 통해 MCP 서버 사용
- GitHub Copilot을 통해 도구와 같은 기능 실행
- MCP 서버를 찾고 관리하도록 Visual Studio Code 구성

## 사용법

MCP 서버는 두 가지 방법으로 제어할 수 있습니다.

- 사용자 인터페이스, 이 장의 뒷부분에서 이 작업을 수행하는 방법을 볼 수 있습니다.
- 터미널, `code` 실행 파일을 사용하여 터미널에서 제어할 수 있습니다.

  사용자 프로필에 MCP 서버를 추가하려면 `--add-mcp` 명령줄 옵션을 사용하고 `{"name":"server-name","command":...}` 형식으로 JSON 서버 구성을 제공합니다.

  ```
  code --add-mcp "{"name":"my-server","command": "uvx","args": ["mcp-server-fetch"]}"
  ```
  <details>
  <summary>스크린샷</summary>

  ![Visual Studio Code의 안내형 MCP 서버 구성](../images/03-GettingStarted/chat-mode-agent.png)
  ![에이전트 세션당 도구 선택](../images/03-GettingStarted/agent-mode-select-tools.png)
  ![MCP 개발 중 오류를 쉽게 디버깅](../images/03-GettingStarted/mcp-list-servers.png)
  </details>

다음 섹션에서 시각적 인터페이스를 사용하는 방법에 대해 자세히 알아보겠습니다.

## 접근 방식

높은 수준에서 접근해야 하는 방법은 다음과 같습니다.

- MCP 서버를 찾기 위한 파일을 구성합니다.
- 해당 서버를 시작/연결하여 기능을 나열합니다.
- GitHub Copilot 채팅 인터페이스를 통해 해당 기능을 사용합니다.

좋습니다. 이제 흐름을 이해했으므로 Visual Studio Code를 통해 MCP 서버를 사용하는 연습을 해보겠습니다.

## 연습: 서버 사용

이 연습에서는 GitHub Copilot 채팅 인터페이스에서 사용할 수 있도록 Visual Studio Code가 MCP 서버를 찾도록 구성합니다.

### -0- 사전 단계, MCP 서버 검색 활성화

MCP 서버 검색을 활성화해야 할 수 있습니다.

1. Visual Studio Code에서 `파일 -> 기본 설정 -> 설정`으로 이동합니다.

1. "MCP"를 검색하고 settings.json 파일에서 `chat.mcp.discovery.enabled`를 활성화합니다.

### -1- 구성 파일 만들기

프로젝트 루트에 구성 파일을 만듭니다. `MCP.json`이라는 파일이 필요하며 `.vscode`라는 폴더에 배치해야 합니다. 다음과 같아야 합니다.

```text
.vscode
|-- mcp.json
```

다음으로 서버 항목을 추가하는 방법을 살펴보겠습니다.

### -2- 서버 구성

*mcp.json*에 다음 내용을 추가합니다.

```json
{
    "inputs": [],
    "servers": {
       "hello-mcp": {
           "command": "node",
           "args": [
               "build/index.js"
           ]
       }
    }
}
```

위는 Node.js로 작성된 서버를 시작하는 간단한 예제입니다. 다른 런타임의 경우 `command` 및 `args`를 사용하여 서버를 시작하는 적절한 명령을 가리킵니다.

### -3- 서버 시작

이제 항목을 추가했으므로 서버를 시작해 보겠습니다.

1. *mcp.json*에서 항목을 찾아 "재생" 아이콘을 찾습니다.

  ![Visual Studio Code에서 서버 시작](./assets/vscode-start-server.png)

1. "재생" 아이콘을 클릭하면 GitHub Copilot 채팅의 도구 아이콘이 사용 가능한 도구 수를 늘리는 것을 볼 수 있습니다. 해당 도구 아이콘을 클릭하면 등록된 도구 목록이 표시됩니다. GitHub Copilot이 컨텍스트로 사용하도록 할지 여부에 따라 각 도구를 선택/선택 취소할 수 있습니다.

  ![Visual Studio Code에서 서버 시작](./assets/vscode-tool.png)

1. 도구를 실행하려면 도구 설명과 일치하는 프롬프트를 입력합니다. 예를 들어 "22에 1을 더하세요"와 같은 프롬프트입니다.

  ![GitHub Copilot에서 도구 실행](./assets/vscode-agent.png)

  23이라는 응답이 표시되어야 합니다.

## 과제

*mcp.json* 파일에 서버 항목을 추가하고 서버를 시작/중지할 수 있는지 확인하십시오. 또한 GitHub Copilot 채팅 인터페이스를 통해 서버의 도구와 통신할 수 있는지 확인하십시오.

## 해결책

[해결책](./solution/README.md)

## 주요 내용

이 장의 주요 내용은 다음과 같습니다.

- Visual Studio Code는 여러 MCP 서버와 해당 도구를 사용할 수 있는 훌륭한 클라이언트입니다.
- GitHub Copilot 채팅 인터페이스는 서버와 상호 작용하는 방법입니다.
- *mcp.json* 파일에 서버 항목을 구성할 때 API 키와 같은 입력을 사용자에게 요청할 수 있습니다.

## 샘플

- [Java 계산기](../samples/java/calculator/README.md)
- [.Net 계산기](../samples/csharp/)
- [JavaScript 계산기](../samples/javascript/README.md)
- [TypeScript 계산기](../samples/typescript/README.md)
- [Python 계산기](../samples/python/)

## 추가 리소스

- [Visual Studio 문서](https://code.visualstudio.com/docs/copilot/chat/mcp-servers)

## 다음 내용

- 다음: [SSE 서버 만들기](../05-sse-server/README.md)