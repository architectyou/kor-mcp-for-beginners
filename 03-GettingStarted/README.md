## 시작하기

이 섹션은 여러 단원으로 구성됩니다:

- **1 첫 번째 서버**, 이 첫 번째 단원에서는 첫 번째 서버를 만들고 검사기 도구로 검사하는 방법을 배웁니다. 이는 서버를 테스트하고 디버그하는 데 유용한 방법입니다. [단원으로 이동](01-first-server/README.md)

- **2 클라이언트**, 이 단원에서는 서버에 연결할 수 있는 클라이언트를 작성하는 방법을 배웁니다. [단원으로 이동](02-client/README.md)

- **3 LLM을 사용한 클라이언트**, 클라이언트를 작성하는 더 좋은 방법은 LLM을 추가하여 서버와 무엇을 할지 "협상"할 수 있도록 하는 것입니다. [단원으로 이동](03-llm-client/README.md)

- **4 Visual Studio Code에서 GitHub Copilot Agent 모드 서버 사용**. 여기서는 Visual Studio Code 내에서 MCP 서버를 실행하는 방법을 살펴봅니다. [단원으로 이동](04-vscode/README.md)

- **5 SSE(Server Sent Events)에서 사용** SSE는 서버-클라이언트 스트리밍을 위한 표준으로, 서버가 HTTP를 통해 클라이언트에 실시간 업데이트를 푸시할 수 있도록 합니다. [단원으로 이동](05-sse-server/README.md)

- **6 MCP를 사용한 HTTP 스트리밍(Streamable HTTP)**. 최신 HTTP 스트리밍, 진행 알림, 그리고 Streamable HTTP를 사용하여 확장 가능하고 실시간 MCP 서버 및 클라이언트를 구현하는 방법을 배웁니다. [단원으로 이동](06-http-streaming/README.md)

- **7 VSCode용 AI 툴킷 활용** MCP 클라이언트 및 서버를 사용하고 테스트합니다. [단원으로 이동](07-aitk/README.md)

- **8 테스트**. 여기서는 서버와 클라이언트를 다양한 방식으로 테스트하는 방법에 특히 중점을 둡니다. [단원으로 이동](08-testing/README.md)

- **9 배포**. 이 장에서는 MCP 솔루션을 배포하는 다양한 방법을 살펴봅니다. [단원으로 이동](09-deployment/README.md)


모델 컨텍스트 프로토콜(MCP)은 애플리케이션이 LLM에 컨텍스트를 제공하는 방법을 표준화하는 개방형 프로토콜입니다. MCP를 AI 애플리케이션을 위한 USB-C 포트와 같다고 생각하십시오. AI 모델을 다양한 데이터 소스 및 도구에 연결하는 표준화된 방법을 제공합니다.

## 학습 목표

이 단원을 마치면 다음을 수행할 수 있습니다:

- C#, Java, Python, TypeScript 및 JavaScript에서 MCP 개발 환경 설정
- 사용자 지정 기능(리소스, 프롬프트 및 도구)을 사용하여 기본 MCP 서버 구축 및 배포
- MCP 서버에 연결하는 호스트 애플리케이션 생성
- MCP 구현 테스트 및 디버그
- 일반적인 설정 문제 및 해결 방법 이해
- MCP 구현을 인기 있는 LLM 서비스에 연결

## MCP 환경 설정

MCP 작업을 시작하기 전에 개발 환경을 준비하고 기본 워크플로우를 이해하는 것이 중요합니다. 이 섹션에서는 MCP를 원활하게 시작할 수 있도록 초기 설정 단계를 안내합니다.

### 전제 조건

MCP 개발에 뛰어들기 전에 다음을 확인하십시오:

- **개발 환경**: 선택한 언어(C#, Java, Python, TypeScript 또는 JavaScript)용
- **IDE/편집기**: Visual Studio, Visual Studio Code, IntelliJ, Eclipse, PyCharm 또는 모든 최신 코드 편집기
- **패키지 관리자**: NuGet, Maven/Gradle, pip 또는 npm/yarn
- **API 키**: 호스트 애플리케이션에서 사용할 계획인 모든 AI 서비스용


### 공식 SDK

다가오는 장에서는 Python, TypeScript, Java 및 .NET을 사용하여 구축된 솔루션을 볼 수 있습니다. 다음은 공식적으로 지원되는 모든 SDK입니다.

MCP는 여러 언어에 대한 공식 SDK를 제공합니다:
- [C# SDK](https://github.com/modelcontextprotocol/csharp-sdk) - Microsoft와 협력하여 유지 관리
- [Java SDK](https://github.com/modelcontextprotocol/java-sdk) - Spring AI와 협력하여 유지 관리
- [TypeScript SDK](https://github.com/modelcontextprotocol/typescript-sdk) - 공식 TypeScript 구현
- [Python SDK](https://github.com/modelcontextprotocol/python-sdk) - 공식 Python 구현
- [Kotlin SDK](https://github.com/modelcontextprotocol/kotlin-sdk) - 공식 Kotlin 구현
- [Swift SDK](https://github.com/modelcontextprotocol/swift-sdk) - Loopwork AI와 협력하여 유지 관리
- [Rust SDK](https://github.com/modelcontextprotocol/rust-sdk) - 공식 Rust 구현

## 핵심 요약

- 언어별 SDK를 사용하면 MCP 개발 환경 설정이 간단합니다.
- MCP 서버 구축에는 명확한 스키마를 사용하여 도구를 생성하고 등록하는 것이 포함됩니다.
- MCP 클라이언트는 서버 및 모델에 연결하여 확장된 기능을 활용합니다.
- 테스트 및 디버깅은 안정적인 MCP 구현에 필수적입니다.
- 배포 옵션은 로컬 개발에서 클라우드 기반 솔루션까지 다양합니다.

## 실습

이 섹션의 모든 장에서 볼 수 있는 연습을 보완하는 샘플 세트가 있습니다. 또한 각 장에는 자체 연습 및 과제가 있습니다.

- [Java 계산기](./samples/java/calculator/README.md)
- [.Net 계산기](./samples/csharp/)
- [JavaScript 계산기](./samples/javascript/README.md)
- [TypeScript 계산기](./samples/typescript/README.md)
- [Python 계산기](./samples/python/)

## 추가 자료

- [Azure에서 모델 컨텍스트 프로토콜을 사용하여 에이전트 구축](https://learn.microsoft.com/azure/developer/ai/intro-agents-mcp)
- [Azure Container Apps를 사용한 원격 MCP (Node.js/TypeScript/JavaScript)](https://learn.microsoft.com/samples/azure-samples/mcp-container-ts/mcp-container-ts/)
- [.NET OpenAI MCP 에이전트](https://learn.microsoft.com/samples/azure-samples/openai-mcp-agent-dotnet/openai-mcp-agent-dotnet/)

## 다음 단계

다음: [첫 번째 MCP 서버 만들기](01-first-server/README.md)
