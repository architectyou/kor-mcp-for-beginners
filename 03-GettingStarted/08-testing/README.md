## 테스트 및 디버깅

MCP 서버를 테스트하기 전에, 사용 가능한 도구와 디버깅을 위한 모범 사례를 이해하는 것이 중요합니다. 효과적인 테스트는 서버가 기대한 대로 동작하는지 확인하고, 문제를 신속하게 식별 및 해결하는 데 도움이 됩니다. 다음 섹션에서는 MCP 구현을 검증하기 위한 권장 접근 방식을 안내합니다.

## 개요

이 레슨에서는 올바른 테스트 접근 방식과 가장 효과적인 테스트 도구를 선택하는 방법을 다룹니다.

## 학습 목표

이 레슨을 마치면 다음을 할 수 있습니다:

- 다양한 테스트 접근 방식을 설명할 수 있다.
- 다양한 도구를 사용하여 코드를 효과적으로 테스트할 수 있다.


## MCP 서버 테스트하기

MCP는 서버를 테스트하고 디버깅하는 데 도움이 되는 도구를 제공합니다:

- **MCP Inspector**: CLI 도구이자 시각적 도구로 실행할 수 있는 커맨드라인 도구입니다.
- **수동 테스트**: curl과 같은 도구를 사용하여 웹 요청을 실행할 수 있으며, HTTP를 실행할 수 있는 모든 도구가 가능합니다.
- **단위 테스트**: 선호하는 테스트 프레임워크를 사용하여 서버와 클라이언트의 기능을 테스트할 수 있습니다.

### MCP Inspector 사용하기

이 도구의 사용법은 이전 레슨에서 설명했지만, 여기서 다시 한 번 간단히 소개합니다. 이 도구는 Node.js로 만들어졌으며, `npx` 실행 파일을 통해 사용할 수 있습니다. 이 명령은 도구를 임시로 다운로드 및 설치하고, 실행이 끝나면 자동으로 정리합니다.

[MCP Inspector](https://github.com/modelcontextprotocol/inspector)는 다음과 같은 기능을 제공합니다:

- **서버 기능 탐색**: 사용 가능한 리소스, 도구, 프롬프트를 자동으로 감지
- **도구 실행 테스트**: 다양한 파라미터로 도구를 실행하고 실시간으로 응답 확인
- **서버 메타데이터 보기**: 서버 정보, 스키마, 설정 등 확인

일반적인 실행 예시는 다음과 같습니다:

```bash
npx @modelcontextprotocol/inspector node build/index.js
```

위 명령을 실행하면 MCP와 시각적 인터페이스가 시작되며, 브라우저에서 로컬 웹 인터페이스가 열립니다. 대시보드에서는 등록된 MCP 서버, 사용 가능한 도구, 리소스, 프롬프트를 확인할 수 있습니다. 인터페이스를 통해 도구 실행을 인터랙티브하게 테스트하고, 서버 메타데이터를 확인하며, 실시간 응답을 볼 수 있어 MCP 서버 구현을 검증하고 디버깅하는 데 용이합니다.

실제 화면 예시: ![Inspector](/03-GettingStarted/01-first-server/assets/connect.png)

이 도구는 CLI 모드로도 실행할 수 있으며, 이 경우 `--cli` 옵션을 추가합니다. 다음은 서버의 모든 도구를 나열하는 "CLI" 모드 실행 예시입니다:

```sh
npx @modelcontextprotocol/inspector --cli node build/index.js --method tools/list
```

### 수동 테스트

Inspector 도구로 서버 기능을 테스트하는 것 외에도, curl과 같은 HTTP 클라이언트를 사용하여 직접 MCP 서버를 테스트할 수 있습니다.

curl을 사용하면 MCP 서버에 HTTP 요청을 직접 보낼 수 있습니다:

```bash
# 예시: 서버 메타데이터 테스트
curl http://localhost:3000/v1/metadata

# 예시: 도구 실행
curl -X POST http://localhost:3000/v1/tools/execute \
  -H "Content-Type: application/json" \
  -d '{"name": "calculator", "parameters": {"expression": "2+2"}}'
```

위 curl 사용 예시처럼, POST 요청을 통해 도구를 호출할 수 있으며, 페이로드에는 도구 이름과 파라미터가 포함됩니다. 자신에게 가장 적합한 방식을 사용하세요. CLI 도구는 일반적으로 더 빠르고, 스크립트화가 쉬워 CI/CD 환경에서 유용합니다.

### 단위 테스트

도구와 리소스가 기대한 대로 동작하는지 확인하기 위해 단위 테스트를 작성하세요. 다음은 예시 테스트 코드입니다.

```python
import pytest

from mcp.server.fastmcp import FastMCP
from mcp.shared.memory import (
    create_connected_server_and_client_session as create_session,
)

# 모듈 전체를 async 테스트로 표시
pytestmark = pytest.mark.anyio


async def test_list_tools_cursor_parameter():
    """list_tools에서 cursor 파라미터가 허용되는지 테스트합니다.

    참고: FastMCP는 현재 페이지네이션을 구현하지 않으므로,
    이 테스트는 클라이언트가 cursor 파라미터를 허용하는지만 검증합니다.
    """

    server = FastMCP("test")

    # 테스트용 도구 2개 생성
    @server.tool(name="test_tool_1")
    async def test_tool_1() -> str:
        """첫 번째 테스트 도구"""
        return "Result 1"

    @server.tool(name="test_tool_2")
    async def test_tool_2() -> str:
        """두 번째 테스트 도구"""
        return "Result 2"

    async with create_session(server._mcp_server) as client_session:
        # cursor 파라미터 없이 테스트 (생략)
        result1 = await client_session.list_tools()
        assert len(result1.tools) == 2

        # cursor=None으로 테스트
        result2 = await client_session.list_tools(cursor=None)
        assert len(result2.tools) == 2

        # cursor를 문자열로 테스트
        result3 = await client_session.list_tools(cursor="some_cursor_value")
        assert len(result3.tools) == 2

        # 빈 문자열 cursor로 테스트
        result4 = await client_session.list_tools(cursor="")
        assert len(result4.tools) == 2
    
```

위 코드는 다음을 수행합니다:

- pytest 프레임워크를 활용하여 테스트 함수와 assert 문을 사용합니다.
- MCP 서버를 생성하고 두 개의 도구를 등록합니다.
- assert 문으로 특정 조건이 충족되는지 확인합니다.

[전체 파일은 여기에서 확인하세요](https://github.com/modelcontextprotocol/python-sdk/blob/main/tests/client/test_list_methods_cursor.py)

위와 같은 방식으로, 자신의 서버를 테스트하여 기능이 제대로 생성되는지 확인할 수 있습니다.

모든 주요 SDK에는 이와 유사한 테스트 섹션이 있으므로, 사용하는 런타임에 맞게 조정할 수 있습니다.

## 샘플

- [Java 계산기](../samples/java/calculator/README.md)
- [.Net 계산기](../samples/csharp/)
- [JavaScript 계산기](../samples/javascript/README.md)
- [TypeScript 계산기](../samples/typescript/README.md)
- [Python 계산기](../samples/python/)

## 추가 자료

- [Python SDK](https://github.com/modelcontextprotocol/python-sdk)

## 다음 단계

- 다음: [배포](../09-deployment/README.md)