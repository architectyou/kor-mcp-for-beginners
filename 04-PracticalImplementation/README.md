# 실전 구현

실전 구현은 Model Context Protocol(MCP)의 힘이 실제로 드러나는 부분입니다. MCP의 이론과 아키텍처를 이해하는 것도 중요하지만, 진정한 가치는 이러한 개념을 실제로 적용하여 현실 세계의 문제를 해결하는 솔루션을 구축, 테스트, 배포할 때 나타납니다. 이 장은 개념적 지식과 실습 개발 사이의 간극을 메우며, MCP 기반 애플리케이션을 실제로 구현하는 과정을 안내합니다.

지능형 어시스턴트 개발, AI의 비즈니스 워크플로우 통합, 데이터 처리용 맞춤형 도구 구축 등 어떤 목적이든 MCP는 유연한 기반을 제공합니다. 언어에 구애받지 않는 설계와 인기 프로그래밍 언어용 공식 SDK 덕분에 다양한 개발자가 쉽게 접근할 수 있습니다. 이러한 SDK를 활용하면 다양한 플랫폼과 환경에서 솔루션을 빠르게 프로토타입, 반복, 확장할 수 있습니다.

다음 섹션에서는 실전 예제, 샘플 코드, 배포 전략 등을 통해 C#, Java, TypeScript, JavaScript, Python에서 MCP를 어떻게 구현하는지 보여줍니다. 또한 MCP 서버를 디버깅 및 테스트하는 방법, API 관리, Azure를 활용한 클라우드 배포 방법도 배울 수 있습니다. 이 실습 자료들은 여러분의 학습 속도를 높이고, 신뢰성 높은 MCP 애플리케이션을 자신 있게 구축할 수 있도록 도와줍니다.

## 개요

이 레슨은 여러 프로그래밍 언어에서 MCP를 실전적으로 구현하는 데 초점을 맞춥니다. C#, Java, TypeScript, JavaScript, Python의 MCP SDK를 활용해 견고한 애플리케이션을 구축하고, MCP 서버를 디버깅 및 테스트하며, 재사용 가능한 리소스, 프롬프트, 도구를 만드는 방법을 살펴봅니다.

## 학습 목표

이 레슨을 마치면 다음을 할 수 있습니다:
- 다양한 언어의 공식 SDK로 MCP 솔루션 구현
- MCP 서버를 체계적으로 디버깅 및 테스트
- 서버 기능(리소스, 프롬프트, 도구) 생성 및 활용
- 복잡한 작업을 위한 효과적인 MCP 워크플로우 설계
- 성능과 신뢰성을 고려한 MCP 구현 최적화

## 공식 SDK 자료

Model Context Protocol은 여러 언어용 공식 SDK를 제공합니다:

- [C# SDK](https://github.com/modelcontextprotocol/csharp-sdk)
- [Java SDK](https://github.com/modelcontextprotocol/java-sdk)
- [TypeScript SDK](https://github.com/modelcontextprotocol/typescript-sdk)
- [Python SDK](https://github.com/modelcontextprotocol/python-sdk)
- [Kotlin SDK](https://github.com/modelcontextprotocol/kotlin-sdk)

## MCP SDK 활용하기

이 섹션에서는 여러 프로그래밍 언어에서 MCP를 구현하는 실전 예제를 제공합니다. 각 언어별 샘플 코드는 `samples` 디렉터리에 정리되어 있습니다.

### 제공 샘플

저장소에는 다음 언어별 [샘플 구현](./samples/)이 포함되어 있습니다:

- [C#](./samples/csharp/README.md)
- [Java](./samples/java/containerapp/README.md)
- [TypeScript](./samples/typescript/README.md)
- [JavaScript](./samples/javascript/README.md)
- [Python](./samples/python/README.md)

각 샘플은 해당 언어 및 생태계에 맞는 주요 MCP 개념과 구현 패턴을 보여줍니다.

## 핵심 서버 기능

MCP 서버는 다음 기능을 조합하여 구현할 수 있습니다:

### 리소스(Resources)
리소스는 사용자 또는 AI 모델이 사용할 수 있는 컨텍스트와 데이터를 제공합니다:
- 문서 저장소
- 지식 베이스
- 구조화된 데이터 소스
- 파일 시스템

### 프롬프트(Prompts)
프롬프트는 사용자용 템플릿 메시지 및 워크플로우입니다:
- 사전 정의된 대화 템플릿
- 안내형 상호작용 패턴
- 특화된 대화 구조

### 도구(Tools)
도구는 AI 모델이 실행할 수 있는 함수입니다:
- 데이터 처리 유틸리티
- 외부 API 연동
- 계산 기능
- 검색 기능

## 샘플 구현: C#

공식 C# SDK 저장소에는 MCP의 다양한 측면을 보여주는 여러 샘플 구현이 포함되어 있습니다:

- **기본 MCP 클라이언트**: MCP 클라이언트를 생성하고 도구를 호출하는 간단한 예제
- **기본 MCP 서버**: 기본 도구 등록이 포함된 최소 서버 구현
- **고급 MCP 서버**: 도구 등록, 인증, 오류 처리가 포함된 완전한 서버
- **ASP.NET 통합**: ASP.NET Core와의 통합 예제
- **도구 구현 패턴**: 다양한 복잡도의 도구 구현 패턴

C# SDK는 프리뷰 단계이며, API는 변경될 수 있습니다. SDK가 발전함에 따라 이 블로그도 계속 업데이트됩니다.

### 주요 기능
- [C# MCP Nuget ModelContextProtocol](https://www.nuget.org/packages/ModelContextProtocol)
- [첫 MCP 서버 만들기](https://devblogs.microsoft.com/dotnet/build-a-model-context-protocol-mcp-server-in-csharp/)

C# 전체 샘플 구현은 [공식 C# SDK 샘플 저장소](https://github.com/modelcontextprotocol/csharp-sdk)에서 확인하세요.

## 샘플 구현: Java

Java SDK는 엔터프라이즈급 기능을 갖춘 강력한 MCP 구현 옵션을 제공합니다.

### 주요 기능
- Spring Framework 통합
- 강력한 타입 안정성
- 리액티브 프로그래밍 지원
- 포괄적인 오류 처리

자세한 Java 구현 샘플은 samples 디렉터리의 [Java 샘플](samples/java/containerapp/README.md)을 참고하세요.

## 샘플 구현: JavaScript

JavaScript SDK는 MCP 구현을 위한 경량의 유연한 접근 방식을 제공합니다.

### 주요 기능
- Node.js 및 브라우저 지원
- Promise 기반 API
- Express 등 프레임워크와의 쉬운 통합
- 스트리밍을 위한 WebSocket 지원

자세한 JavaScript 구현 샘플은 samples 디렉터리의 [JavaScript 샘플](samples/javascript/README.md)을 참고하세요.

## 샘플 구현: Python

Python SDK는 파이썬다운 방식으로 MCP를 구현하며, ML 프레임워크와의 뛰어난 통합을 제공합니다.

### 주요 기능
- asyncio 기반 async/await 지원
- Flask 및 FastAPI 통합
- 간단한 도구 등록
- 인기 ML 라이브러리와의 네이티브 통합

자세한 Python 구현 샘플은 samples 디렉터리의 [Python 샘플](samples/python/README.md)을 참고하세요.

## API 관리

Azure API Management는 MCP 서버를 보호하는 데 매우 유용합니다. Azure API Management 인스턴스를 MCP 서버 앞에 두고, 다음과 같은 기능을 활용할 수 있습니다:

- 속도 제한
- 토큰 관리
- 모니터링
- 부하 분산
- 보안

### Azure 샘플

아래는 실제로 MCP 서버를 만들고 Azure API Management로 보호하는 [Azure 샘플](https://github.com/Azure-Samples/remote-mcp-apim-functions-python)입니다.

아래 이미지는 인증 흐름을 보여줍니다:

![APIM-MCP](https://github.com/Azure-Samples/remote-mcp-apim-functions-python/blob/main/mcp-client-authorization.gif?raw=true)

위 이미지에서 다음이 이루어집니다:
- Microsoft Entra를 통한 인증/인가
- Azure API Management가 게이트웨이 역할 및 정책 적용
- Azure Monitor가 모든 요청을 로깅

#### 인증 흐름

인증 흐름을 좀 더 자세히 살펴보면:

![시퀀스 다이어그램](https://github.com/Azure-Samples/remote-mcp-apim-functions-python/blob/main/infra/app/apim-oauth/diagrams/images/mcp-client-auth.png?raw=true)

#### MCP 인증 명세

[MCP 인증 명세](https://modelcontextprotocol.io/specification/2025-03-26/basic/authorization#2-10-third-party-authorization-flow)에서 더 알아보세요.

## 원격 MCP 서버를 Azure에 배포하기

앞서 언급한 샘플을 배포해봅시다:

1. 저장소 클론

    ```bash
    git clone https://github.com/Azure-Samples/remote-mcp-apim-functions-python.git
    cd remote-mcp-apim-functions-python
    ```

2. `Microsoft.App` 리소스 공급자 등록
    * Azure CLI: `az provider register --namespace Microsoft.App --wait`
    * Azure PowerShell: `Register-AzResourceProvider -ProviderNamespace Microsoft.App` 후, 일정 시간 후 `(Get-AzResourceProvider -ProviderNamespace Microsoft.App).RegistrationState`로 등록 완료 확인

3. [azd](https://aka.ms/azd) 명령어로 API Management, 함수 앱(코드 포함), 기타 필요한 Azure 리소스 프로비저닝

    ```shell
    azd up
    ```

    이 명령어로 모든 클라우드 리소스가 Azure에 배포됩니다.

### MCP Inspector로 서버 테스트하기

1. **새 터미널 창**에서 MCP Inspector 설치 및 실행

    ```shell
    npx @modelcontextprotocol/inspector
    ```

    다음과 같은 인터페이스가 나타납니다:

    ![Node inspector 연결](/03-GettingStarted/01-first-server/assets/connect.png)

2. 앱에서 표시된 URL(예: http://127.0.0.1:6274/#resources)로 MCP Inspector 웹 앱을 엽니다.
3. 전송 타입을 `SSE`로 설정
4. `azd up` 실행 후 표시되는 API Management SSE 엔드포인트를 URL로 입력 후 **Connect**:

    ```shell
    https://<azd 출력의 apim-servicename>.azure-api.net/mcp/sse
    ```

5. **도구 목록**에서 도구를 클릭하고 **Run Tool**을 실행

모든 단계가 정상적으로 진행되면 MCP 서버에 연결되어 도구를 호출할 수 있습니다.

## Azure용 MCP 서버

[Remote-mcp-functions](https://github.com/Azure-Samples/remote-mcp-functions-dotnet): 이 저장소들은 Python, C# .NET, Node/TypeScript로 Azure Functions를 활용한 원격 MCP 서버 구축 및 배포를 위한 퀵스타트 템플릿입니다.

샘플은 다음을 제공합니다:
- 로컬에서 빌드 및 실행: 로컬에서 MCP 서버 개발 및 디버깅
- Azure에 배포: `azd up` 명령으로 클라우드에 손쉽게 배포
- 다양한 클라이언트에서 연결: VS Code Copilot 에이전트 모드, MCP Inspector 등에서 MCP 서버 연결

### 주요 기능
- 설계 단계부터 보안: 키와 HTTPS로 MCP 서버 보호
- 인증 옵션: 내장 인증 및/또는 API Management를 통한 OAuth 지원
- 네트워크 격리: Azure Virtual Networks(VNET)로 네트워크 격리 지원
- 서버리스 아키텍처: Azure Functions 기반 확장성 높은 이벤트 중심 실행
- 로컬 개발: 포괄적인 로컬 개발 및 디버깅 지원
- 간편한 배포: Azure로의 간소화된 배포 프로세스

필요한 모든 구성 파일, 소스 코드, 인프라 정의가 포함되어 있어, 프로덕션 수준의 MCP 서버 구현을 빠르게 시작할 수 있습니다.

- [Azure Remote MCP Functions Python](https://github.com/Azure-Samples/remote-mcp-functions-python) - Python 기반 Azure Functions로 MCP 구현 샘플
- [Azure Remote MCP Functions .NET](https://github.com/Azure-Samples/remote-mcp-functions-dotnet) - C# .NET 기반 Azure Functions로 MCP 구현 샘플
- [Azure Remote MCP Functions Node/Typescript](https://github.com/Azure-Samples/remote-mcp-functions-typescript) - Node/TypeScript 기반 Azure Functions로 MCP 구현 샘플

## 핵심 요약

- MCP SDK는 견고한 MCP 솔루션 구현을 위한 언어별 도구를 제공
- 디버깅 및 테스트 과정은 신뢰성 높은 MCP 애플리케이션에 필수
- 재사용 가능한 프롬프트 템플릿으로 일관된 AI 상호작용 가능
- 잘 설계된 워크플로우로 여러 도구를 활용한 복잡한 작업 오케스트레이션 가능
- MCP 구현 시 보안, 성능, 오류 처리 고려 필요

## 실습

여러분의 도메인에서 실제 문제를 해결할 수 있는 실전 MCP 워크플로우를 설계해보세요:

1. 이 문제를 해결하는 데 유용할 3~4개의 도구를 식별하세요
