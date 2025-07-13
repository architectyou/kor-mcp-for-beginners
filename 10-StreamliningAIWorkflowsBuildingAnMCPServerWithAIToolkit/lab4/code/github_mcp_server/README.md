# 날씨 MCP 서버

이것은 모의 응답으로 날씨 도구를 구현하는 Python 샘플 MCP 서버입니다. 자체 MCP 서버를 위한 스캐폴드로 사용할 수 있습니다. 다음 기능을 포함합니다:

- **날씨 도구**: 주어진 위치를 기반으로 모의 날씨 정보를 제공하는 도구입니다.
- **Git 클론 도구**: Git 리포지토리를 지정된 폴더로 클론하는 도구입니다.
- **VS Code 열기 도구**: VS Code 또는 VS Code Insiders에서 폴더를 여는 도구입니다.
- **에이전트 빌더에 연결**: 테스트 및 디버깅을 위해 MCP 서버를 에이전트 빌더에 연결할 수 있는 기능입니다.
- **[MCP Inspector](https://github.com/modelcontextprotocol/inspector)에서 디버그**: MCP Inspector를 사용하여 MCP 서버를 디버그할 수 있는 기능입니다.

## 날씨 MCP 서버 템플릿 시작하기

> **전제 조건**
>
> 로컬 개발 머신에서 MCP 서버를 실행하려면 다음이 필요합니다:
>
> - [Python](https://www.python.org/)
> - [Git](https://git-scm.com/) (git_clone_repo 도구에 필요)
> - [VS Code](https://code.visualstudio.com/) 또는 [VS Code Insiders](https://code.visualstudio.com/insiders/) (open_in_vscode 도구에 필요)
> - (*선택 사항 - uv를 선호하는 경우*) [uv](https://github.com/astral-sh/uv)
> - [Python 디버거 확장](https://marketplace.visualstudio.com/items?itemName=ms-python.debugpy)

## 환경 준비

이 프로젝트의 환경을 설정하는 두 가지 접근 방식이 있습니다. 선호도에 따라 둘 중 하나를 선택할 수 있습니다.

> 참고: 가상 환경 Python이 생성된 후 사용되는지 확인하려면 VSCode 또는 터미널을 다시 로드하십시오.

| 접근 방식 | 단계 |
| -------- | ----- |
| `uv` 사용 | 1. 가상 환경 생성: `uv venv` <br>2. VSCode 명령 "***Python: 인터프리터 선택***"을 실행하고 생성된 가상 환경에서 Python을 선택합니다. <br>3. 종속성 설치 (개발 종속성 포함): `uv pip install -r pyproject.toml --extra dev` |
| `pip` 사용 | 1. 가상 환경 생성: `python -m venv .venv` <br>2. VSCode 명령 "***Python: 인터프리터 선택***"을 실행하고 생성된 가상 환경에서 Python을 선택합니다.<br>3. 종속성 설치 (개발 종속성 포함): `pip install -e .[dev]` | 

환경 설정 후, MCP 클라이언트로서 에이전트 빌더를 통해 로컬 개발 머신에서 서버를 실행하여 시작할 수 있습니다:
1. VS Code 디버그 패널을 엽니다. `Debug in Agent Builder`를 선택하거나 `F5`를 눌러 MCP 서버 디버깅을 시작합니다.
2. AI 툴킷 에이전트 빌더를 사용하여 [이 프롬프트](vscode://ms-windows-ai-studio.windows-ai-studio/open_prompt_builder?model_id=github/gpt-4o-mini&system_prompt=You%20are%20a%20weather%20forecast%20professional%20that%20can%20tell%20weather%20information%20based%20on%20given%20location&user_prompt=What%20is%20the%20weather%20in%20Shanghai?&track_from=vsc_md&mcp=github_mcp_server)로 서버를 테스트합니다. 서버는 에이전트 빌더에 자동으로 연결됩니다.
3. `실행`을 클릭하여 프롬프트로 서버를 테스트합니다.

**축하합니다**! MCP 클라이언트로서 에이전트 빌더를 통해 로컬 개발 머신에서 날씨 MCP 서버를 성공적으로 실행했습니다.
![DebugMCP](https://raw.githubusercontent.com/microsoft/windows-ai-studio-templates/refs/heads/dev/mcpServers/mcp_debug.gif)

## 템플릿에 포함된 내용

| 폴더 / 파일| 내용 |
| ------------ | -------------------------------------------- |
| `.vscode`    | 디버깅을 위한 VSCode 파일 |
| `.aitk`      | AI 툴킷 구성 |
| `src`        | 날씨 MCP 서버의 소스 코드 |

## 날씨 MCP 서버 디버그 방법

> 참고:
> - [MCP Inspector](https://github.com/modelcontextprotocol/inspector)는 MCP 서버를 테스트하고 디버깅하기 위한 시각적 개발자 도구입니다.
> - 모든 디버깅 모드는 중단점을 지원하므로 도구 구현 코드에 중단점을 추가할 수 있습니다.

## 사용 가능한 도구

### 날씨 도구
`get_weather` 도구는 지정된 위치에 대한 모의 날씨 정보를 제공합니다.

| 매개변수 | 유형 | 설명 |
| --------- | ---- | ----------- |
| `location` | 문자열 | 날씨를 가져올 위치 (예: 도시 이름, 주 또는 좌표) |

### Git 클론 도구
`git_clone_repo` 도구는 Git 리포지토리를 지정된 폴더로 클론합니다.

| 매개변수 | 유형 | 설명 |
| --------- | ---- | ----------- |
| `repo_url` | 문자열 | 클론할 Git 리포지토리의 URL |
| `target_folder` | 문자열 | 리포지토리가 클론될 폴더의 경로 |

이 도구는 다음을 포함하는 JSON 객체를 반환합니다:
- `success`: 작업 성공 여부를 나타내는 부울
- `target_folder` 또는 `error`: 클론된 리포지토리의 경로 또는 오류 메시지

### VS Code 열기 도구
`open_in_vscode` 도구는 VS Code 또는 VS Code Insiders 애플리케이션에서 폴더를 엽니다.

| 매개변수 | 유형 | 설명 |
| --------- | ---- | ----------- |
| `folder_path` | 문자열 | 열 폴더의 경로 |
| `use_insiders` | 부울 (선택 사항) | 일반 VS Code 대신 VS Code Insiders를 사용할지 여부 |

이 도구는 다음을 포함하는 JSON 객체를 반환합니다:
- `success`: 작업 성공 여부를 나타내는 부울
- `message` 또는 `error`: 확인 메시지 또는 오류 메시지

## 디버그 모드 | 설명 | 디버그 단계 |
| ---------- | ----------- | --------------- |
| 에이전트 빌더 | AI 툴킷을 통해 에이전트 빌더에서 MCP 서버를 디버그합니다. | 1. VS Code 디버그 패널을 엽니다. `Debug in Agent Builder`를 선택하고 `F5`를 눌러 MCP 서버 디버깅을 시작합니다.<br>2. AI 툴킷 에이전트 빌더를 사용하여 [이 프롬프트](vscode://ms-windows-ai-studio.windows-ai-studio/open_prompt_builder?model_id=github/gpt-4o-mini&system_prompt=You%20are%20a%20weather%20forecast%20professional%20that%20can%20tell%20weather%20information%20based%20on%20given%20location&user_prompt=What%20is%20the%20weather%20in%20Shanghai?&track_from=vsc_md&mcp=github_mcp_server)로 서버를 테스트합니다. 서버는 에이전트 빌더에 자동으로 연결됩니다.<br>3. `실행`을 클릭하여 프롬프트로 서버를 테스트합니다. |
| MCP Inspector | MCP Inspector를 사용하여 MCP 서버를 디버그합니다. | 1. [Node.js](https://nodejs.org/) 설치<br> 2. Inspector 설정: `cd inspector` && `npm install` <br> 3. VS Code 디버그 패널을 엽니다. `Debug SSE in Inspector (Edge)` 또는 `Debug SSE in Inspector (Chrome)`을 선택합니다. F5를 눌러 디버깅을 시작합니다.<br> 4. 브라우저에서 MCP Inspector가 실행되면 `연결` 버튼을 클릭하여 이 MCP 서버에 연결합니다.<br> 5. 그런 다음 `도구 나열`, 도구 선택, 매개변수 입력 및 `도구 실행`을 통해 서버 코드를 디버그할 수 있습니다.<br> |

## 기본 포트 및 사용자 지정

| 디버그 모드 | 포트 | 정의 | 사용자 지정 | 참고 |
| ---------- | ----- | ------------ | -------------- |-------------- |
| 에이전트 빌더 | 3001 | [tasks.json](.vscode/tasks.json) | 위 포트를 변경하려면 [launch.json](.vscode/launch.json), [tasks.json](.vscode/tasks.json), [\_\_init\_\_.py](src/__init__.py), [mcp.json](.aitk/mcp.json)을 편집합니다. | 해당 없음 |
| MCP Inspector | 3001 (서버); 5173 및 3000 (Inspector) | [tasks.json](.vscode/tasks.json) | 위 포트를 변경하려면 [launch.json](.vscode/launch.json), [tasks.json](.vscode/tasks.json), [\_\_init\_\_.py](src/__init__.py), [mcp.json](.aitk/mcp.json)을 편집합니다. | 해당 없음 |

## 피드백

이 템플릿에 대한 피드백이나 제안 사항이 있으면 [AI 툴킷 GitHub 저장소](https://github.com/microsoft/vscode-ai-toolkit/issues)