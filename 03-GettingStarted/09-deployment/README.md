# MCP 서버 배포

MCP 서버를 배포하면 다른 사람들이 로컬 환경을 넘어 서버의 도구와 리소스에 접근할 수 있습니다. 확장성, 신뢰성 및 관리 용이성에 대한 요구 사항에 따라 고려해야 할 몇 가지 배포 전략이 있습니다. 아래에서는 로컬, 컨테이너 및 클라우드에 MCP 서버를 배포하는 방법에 대한 지침을 찾을 수 있습니다.

## 개요

이 단원에서는 MCP 서버 앱을 배포하는 방법을 다룹니다.

## 학습 목표

이 단원을 마치면 다음을 수행할 수 있습니다:

- 다양한 배포 접근 방식을 평가합니다.
- 앱을 배포합니다.

## 로컬 개발 및 배포

서버가 사용자 기기에서 실행되어 사용될 목적으로 만들어진 경우 다음 단계를 따를 수 있습니다:

1. **서버 다운로드**. 서버를 직접 작성하지 않았다면 먼저 기기에 다운로드합니다.
1. **서버 프로세스 시작**: MCP 서버 애플리케이션을 실행합니다.

SSE의 경우 (stdio 유형 서버에는 필요 없음)

1. **네트워킹 구성**: 서버가 예상 포트에서 접근 가능한지 확인합니다.
1. **클라이언트 연결**: `http://localhost:3000`과 같은 로컬 연결 URL을 사용합니다.

## 클라우드 배포

MCP 서버는 다양한 클라우드 플랫폼에 배포할 수 있습니다:

- **서버리스 함수**: 경량 MCP 서버를 서버리스 함수로 배포합니다.
- **컨테이너 서비스**: Azure Container Apps, AWS ECS 또는 Google Cloud Run과 같은 서비스를 사용합니다.
- **Kubernetes**: 고가용성을 위해 Kubernetes 클러스터에 MCP 서버를 배포하고 관리합니다.

### 예시: Azure Container Apps

Azure Container Apps는 MCP 서버 배포를 지원합니다. 아직 작업 중이며 현재 SSE 서버를 지원합니다.

다음은 진행 방법입니다:

1. 저장소 복제:

  ```sh
  git clone https://github.com/anthonychu/azure-container-apps-mcp-sample.git
  ```

1. 로컬에서 실행하여 테스트:

  ```sh
  uv venv
  uv sync

  # linux/macOS
  export API_KEYS=<AN_API_KEY>
  # windows
  set API_KEYS=<AN_API_KEY>

  uv run fastapi dev main.py
  ```

1. 로컬에서 시도하려면 *.vscode* 디렉토리에 *mcp.json* 파일을 생성하고 다음 내용을 추가합니다:

  ```json
  {
      "inputs": [
          {
              "type": "promptString",
              "id": "weather-api-key",
              "description": "Weather API Key",
              "password": true
          }
      ],
      "servers": {
          "weather-sse": {
              "type": "sse",
              "url": "http://localhost:8000/sse",
              "headers": {
                  "x-api-key": "${input:weather-api-key}"
              }
          }
      }
  }
  ```

  SSE 서버가 시작되면 JSON 파일에서 재생 아이콘을 클릭하면 GitHub Copilot이 서버의 도구를 인식하고 도구 아이콘이 표시됩니다.

1. 배포하려면 다음 명령을 실행합니다:

  ```sh
  az containerapp up -g <RESOURCE_GROUP_NAME> -n weather-mcp --environment mcp -l westus --env-vars API_KEYS=<AN_API_KEY> --source .
  ```

이러한 단계를 통해 로컬에 배포하고 Azure에 배포할 수 있습니다.

## 추가 자료

- [Azure Functions + MCP](https://learn.microsoft.com/en-us/samples/azure-samples/remote-mcp-functions-dotnet/remote-mcp-functions-dotnet/)
- [Azure Container Apps 문서](https://techcommunity.microsoft.com/blog/appsonazureblog/host-remote-mcp-servers-in-azure-container-apps/4403550)
- [Azure Container Apps MCP 저장소](https://github.com/anthonychu/azure-container-apps-mcp-sample)

## 다음 단계

- 다음: [실제 구현](../../04-PracticalImplementation/README.md)
