# 🔧 모듈 3: AI 툴킷을 활용한 고급 MCP 개발

![Duration](https://img.shields.io/badge/Duration-20_minutes-blue?style=flat-square)
![AI Toolkit](https://img.shields.io/badge/AI_Toolkit-Required-orange?style=flat-square)
![Python](https://img.shields.io/badge/Python-3.10+-green?style=flat-square)
![MCP SDK](https://img.shields.io/badge/MCP_SDK-1.9.3-purple?style=flat-square)
![Inspector](https://img.shields.io/badge/Inspector-0.14.0-blue?style=flat-square)

## 🎯 학습 목표

이 랩을 마치면 다음을 수행할 수 있습니다:

- ✅ AI 툴킷을 사용하여 사용자 지정 MCP 서버 생성
- ✅ 최신 MCP Python SDK (v1.9.3) 구성 및 사용
- ✅ 디버깅을 위한 MCP Inspector 설정 및 활용
- ✅ Agent Builder 및 Inspector 환경 모두에서 MCP 서버 디버깅
- ✅ 고급 MCP 서버 개발 워크플로우 이해

## 📋 전제 조건

- 랩 2 (MCP 기본 사항) 완료
- AI 툴킷 확장 프로그램이 설치된 VS Code
- Python 3.10+ 환경
- Inspector 설정을 위한 Node.js 및 npm

## 🏗️ 만들게 될 것

이 랩에서는 다음을 시연하는 **날씨 MCP 서버**를 만들게 됩니다:
- 사용자 지정 MCP 서버 구현
- AI 툴킷 Agent Builder와의 통합
- 전문적인 디버깅 워크플로우
- 최신 MCP SDK 사용 패턴

---

## 🔧 핵심 구성 요소 개요

### 🐍 MCP Python SDK
Model Context Protocol Python SDK는 사용자 지정 MCP 서버를 구축하기 위한 기반을 제공합니다. 향상된 디버깅 기능을 갖춘 버전 1.9.3을 사용하게 됩니다.

### 🔍 MCP Inspector
강력한 디버깅 도구로 다음을 제공합니다:
- 실시간 서버 모니터링
- 도구 실행 시각화
- 네트워크 요청/응답 검사
- 대화형 테스트 환경

---

## 📖 단계별 구현

### 1단계: Agent Builder에서 WeatherAgent 생성

1. AI 툴킷 확장 프로그램을 통해 VS Code에서 **Agent Builder 실행**
2. 다음 구성으로 **새 에이전트 생성**:
   - 에이전트 이름: `WeatherAgent`

![Agent Creation](../../images/10-StreamliningAIWorkflowsBuildingAnMCPServerWithAIToolkit/lab3/Agent.png)

### 2단계: MCP 서버 프로젝트 초기화

1. Agent Builder에서 **도구** → **도구 추가**로 이동
2. 사용 가능한 옵션에서 **"MCP 서버" 선택**
3. **"새 MCP 서버 생성" 선택**
4. **`python-weather` 템플릿 선택**
5. **서버 이름 지정**: `weather_mcp`

![Python Template Selection](../../images/10-StreamliningAIWorkflowsBuildingAnMCPServerWithAIToolkit/lab3/Pythontemplate.png)

### 3단계: 프로젝트 열기 및 검토

1. VS Code에서 **생성된 프로젝트 열기**
2. **프로젝트 구조 검토**:
   ```
   weather_mcp/
   ├── src/
   │   ├── __init__.py
   │   └── server.py
   ├── inspector/
   │   ├── package.json
   │   └── package-lock.json
   ├── .vscode/
   │   ├── launch.json
   │   └── tasks.json
   ├── pyproject.toml
   └── README.md
   ```

### 4단계: 최신 MCP SDK로 업그레이드

> **🔍 왜 업그레이드해야 할까요?** 향상된 기능과 더 나은 디버깅 기능을 위해 최신 MCP SDK (v1.9.3) 및 Inspector 서비스 (0.14.0)를 사용하고자 합니다.

#### 4a. Python 종속성 업데이트

**`pyproject.toml` 편집**: [./code/weather_mcp/pyproject.toml](./code/weather_mcp/pyproject.toml) 업데이트

#### 4b. Inspector 구성 업데이트

**`inspector/package.json` 편집**: [./code/weather_mcp/inspector/package.json](./code/weather_mcp/inspector/package.json) 업데이트

#### 4c. Inspector 종속성 업데이트

**`inspector/package-lock.json` 편집**: [./code/weather_mcp/inspector/package-lock.json](./code/weather_mcp/inspector/package-lock.json) 업데이트

> **📝 참고**: 이 파일에는 광범위한 종속성 정의가 포함되어 있습니다. 아래는 필수 구조이며, 전체 내용은 적절한 종속성 해결을 보장합니다.

> **⚡ 전체 패키지 잠금**: 전체 package-lock.json에는 약 3000줄의 종속성 정의가 포함되어 있습니다. 위는 주요 구조를 보여주며, 제공된 파일은 완전한 종속성 해결을 위한 것입니다.

### 5단계: VS Code 디버깅 구성

*참고: 지정된 경로의 파일을 복사하여 해당 로컬 파일을 대체하십시오.*

#### 5a. 시작 구성 업데이트

**`.vscode/launch.json` 편집**:

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Attach to Local MCP",
      "type": "debugpy",
      "request": "attach",
      "connect": {
        "host": "localhost",
        "port": 5678
      },
      "presentation": {
        "hidden": true
      },
      "internalConsoleOptions": "neverOpen",
      "postDebugTask": "Terminate All Tasks"
    },
    {
      "name": "Launch Inspector (Edge)",
      "type": "msedge",
      "request": "launch",
      "url": "http://localhost:6274?timeout=60000&serverUrl=http://localhost:3001/sse#tools",
      "cascadeTerminateToConfigurations": [
        "Attach to Local MCP"
      ],
      "presentation": {
        "hidden": true
      },
      "internalConsoleOptions": "neverOpen"
    },
    {
      "name": "Launch Inspector (Chrome)",
      "type": "chrome",
      "request": "launch",
      "url": "http://localhost:6274?timeout=60000&serverUrl=http://localhost:3001/sse#tools",
      "cascadeTerminateToConfigurations": [
        "Attach to Local MCP"
      ],
      "presentation": {
        "hidden": true
      },
      "internalConsoleOptions": "neverOpen"
    }
  ],
  "compounds": [
    {
      "name": "Debug in Agent Builder",
      "configurations": [
        "Attach to Local MCP"
      ],
      "preLaunchTask": "Open Agent Builder",
    },
    {
      "name": "Debug in Inspector (Edge)",
      "configurations": [
        "Launch Inspector (Edge)",
        "Attach to Local MCP"
      ],
      "preLaunchTask": "Start MCP Inspector",
      "stopAll": true
    },
    {
      "name": "Debug in Inspector (Chrome)",
      "configurations": [
        "Launch Inspector (Chrome)",
        "Attach to Local MCP"
      ],
      "preLaunchTask": "Start MCP Inspector",
      "stopAll": true
    }
  ]
}
```

**`.vscode/tasks.json` 편집**:

```
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "Start MCP Server",
      "type": "shell",
      "command": "python -m debugpy --listen 127.0.0.1:5678 src/__init__.py sse",
      "isBackground": true,
      "options": {
        "cwd": "${workspaceFolder}",
        "env": {
          "PORT": "3001"
        }
      },
      "problemMatcher": {
        "pattern": [
          {
            "regexp": "^.*$",
            "file": 0,
            "location": 1,
            "message": 2
          }
        ],
        "background": {
          "activeOnStart": true,
          "beginsPattern": ".*",
          "endsPattern": "Application startup complete|running"
        }
      }
    },
    {
      "label": "Start MCP Inspector",
      "type": "shell",
      "command": "npm run dev:inspector",
      "isBackground": true,
      "options": {
        "cwd": "${workspaceFolder}/inspector",
        "env": {
          "CLIENT_PORT": "6274",
          "SERVER_PORT": "6277",
        }
      },
      "problemMatcher": {
        "pattern": [
          {
            "regexp": "^.*$",
            "file": 0,
            "location": 1,
            "message": 2
          }
        ],
        "background": {
          "activeOnStart": true,
          "beginsPattern": "Starting MCP inspector",
          "endsPattern": "Proxy server listening on port"
        }
      },
      "dependsOn": [
        "Start MCP Server"
      ]
    },
    {
      "label": "Open Agent Builder",
      "type": "shell",
      "command": "echo ${input:openAgentBuilder}",
      "presentation": {
        "reveal": "never"
      },
      "dependsOn": [
        "Start MCP Server"
      ],
    },
    {
      "label": "Terminate All Tasks",
      "command": "echo ${input:terminate}",
      "type": "shell",
      "problemMatcher": []
    }
  ],
  "inputs": [
    {
      "id": "openAgentBuilder",
      "type": "command",
      "command": "ai-mlstudio.agentBuilder",
      "args": {
        "initialMCPs": [ "local-server-weather_mcp" ],
        "triggeredFrom": "vsc-tasks"
      }
    },
    {
      "id": "terminate",
      "type": "command",
      "command": "workbench.action.tasks.terminate",
      "args": "terminateAll"
    }
  ]
}
```

---

## 🚀 MCP 서버 실행 및 테스트

### 6단계: 종속성 설치

구성 변경 후 다음 명령을 실행합니다:

**Python 종속성 설치**:
```bash
uv sync
```

**Inspector 종속성 설치**:
```bash
cd inspector
npm install
```

### 7단계: Agent Builder로 디버깅

1. **F5 키 누르기** 또는 **"Debug in Agent Builder"** 구성 사용
2. 디버그 패널에서 **복합 구성 선택**
3. **서버가 시작되고** Agent Builder가 열릴 때까지 기다립니다.
4. 자연어 쿼리로 **날씨 MCP 서버 테스트**

다음과 같이 프롬프트 입력

SYSTEM_PROMPT

```
You are my weather assistant
```

USER_PROMPT

```
How's the weather like in Seattle
```

![Agent Builder Debug Result](../../images/10-StreamliningAIWorkflowsBuildingAnMCPServerWithAIToolkit/lab3/Result.png)

### 8단계: MCP Inspector로 디버깅

1. **"Debug in Inspector"** 구성 (Edge 또는 Chrome) 사용
2. `http://localhost:6274`에서 **Inspector 인터페이스 열기**
3. **대화형 테스트 환경 탐색**:
   - 사용 가능한 도구 보기
   - 도구 실행 테스트
   - 네트워크 요청 모니터링
   - 서버 응답 디버깅

![MCP Inspector Interface](../../images/10-StreamliningAIWorkflowsBuildingAnMCPServerWithAIToolkit/lab3/Inspector.png)

---

## 🎯 주요 학습 결과

이 랩을 완료함으로써 다음을 수행했습니다:

- [x] AI 툴킷 템플릿을 사용하여 **사용자 지정 MCP 서버 생성**
- [x] 향상된 기능을 위해 **최신 MCP SDK (v1.9.3)로 업그레이드**
- [x] Agent Builder 및 Inspector 모두에 대한 **전문적인 디버깅 워크플로우 구성**
- [x] 대화형 서버 테스트를 위한 **MCP Inspector 설정**
- [x] MCP 개발을 위한 **VS Code 디버깅 구성 마스터**

## 🔧 탐색된 고급 기능

| 기능 | 설명 | 사용 사례 |
|---------|-------------|----------|
| **MCP Python SDK v1.9.3** | 최신 프로토콜 구현 | 최신 서버 개발 |
| **MCP Inspector 0.14.0** | 대화형 디버깅 도구 | 실시간 서버 테스트 |
| **VS Code 디버깅** | 통합 개발 환경 | 전문적인 디버깅 워크플로우 |
| **Agent Builder 통합** | 직접 AI 툴킷 연결 | 엔드투엔드 에이전트 테스트 |

## 📚 추가 자료

- [MCP Python SDK 문서](https://modelcontextprotocol.io/docs/sdk/python)
- [AI 툴킷 확장 가이드](https://code.visualstudio.com/docs/ai/ai-toolkit)
- [VS Code 디버깅 문서](https://code.visualstudio.com/docs/editor/debugging)
- [Model Context Protocol 사양](https://modelcontextprotocol.io/docs/concepts/architecture)

---

**🎉 축하합니다!** 랩 3을 성공적으로 완료했으며, 이제 전문적인 개발 워크플로우를 사용하여 사용자 지정 MCP 서버를 생성, 디버그 및 배포할 수 있습니다.

### 🔜 다음 모듈로 계속

MCP 기술을 실제 개발 워크플로우에 적용할 준비가 되셨습니까? **[모듈 4: 실제 MCP 개발 - 사용자 지정 GitHub 클론 서버](../lab4/README.md)**로 계속 진행하여 다음을 수행합니다:
- GitHub 리포지토리 작업을 자동화하는 프로덕션 준비 MCP 서버 구축
- MCP를 통한 GitHub 리포지토리 클론 기능 구현
- 사용자 지정 MCP 서버를 VS Code 및 GitHub Copilot Agent 모드와 통합
- 프로덕션 환경에서 사용자 지정 MCP 서버 테스트 및 배포
- 개발자를 위한 실용적인 워크플로우 자동화 학습
