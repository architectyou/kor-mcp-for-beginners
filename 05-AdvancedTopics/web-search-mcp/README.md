# 레슨: 웹 검색 MCP 서버 구축

이 장에서는 외부 API와 통합하고, 다양한 데이터 유형을 처리하며, 오류를 관리하고, 여러 도구를 오케스트레이션하는 실제 AI 에이전트를 프로덕션 준비 형식으로 구축하는 방법을 보여줍니다. 다음을 볼 수 있습니다:

- **인증이 필요한 외부 API와의 통합**
- **여러 엔드포인트에서 다양한 데이터 유형 처리**
- **강력한 오류 처리 및 로깅 전략**
- **단일 서버에서 다중 도구 오케스트레이션**

마지막에는 고급 AI 및 LLM 기반 애플리케이션에 필수적인 패턴 및 모범 사례에 대한 실질적인 경험을 얻게 될 것입니다.

## 소개

이 레슨에서는 SerpAPI를 사용하여 LLM 기능을 실시간 웹 데이터로 확장하는 고급 MCP 서버 및 클라이언트를 구축하는 방법을 배웁니다. 이는 웹에서 최신 정보에 액세스할 수 있는 동적 AI 에이전트를 개발하는 데 중요한 기술입니다.

## 학습 목표

이 레슨을 마치면 다음을 수행할 수 있습니다:

- 외부 API(예: SerpAPI)를 MCP 서버에 안전하게 통합
- 웹, 뉴스, 제품 검색 및 Q&A를 위한 여러 도구 구현
- LLM 소비를 위한 구조화된 데이터 구문 분석 및 형식 지정
- 오류를 효과적으로 처리하고 API 속도 제한 관리
- 자동화된 및 대화형 MCP 클라이언트 모두 구축 및 테스트

## 웹 검색 MCP 서버

이 섹션에서는 웹 검색 MCP 서버의 아키텍처 및 기능을 소개합니다. FastMCP와 SerpAPI가 함께 사용되어 LLM 기능을 실시간 웹 데이터로 확장하는 방법을 볼 수 있습니다.

### 개요

이 구현은 MCP가 다양하고 외부 API 기반 작업을 안전하고 효율적으로 처리하는 능력을 보여주는 네 가지 도구를 특징으로 합니다:

- **general_search**: 광범위한 웹 결과용
- **news_search**: 최신 헤드라인용
- **product_search**: 전자 상거래 데이터용
- **qna**: 질문-답변 스니펫용

### 기능
- **코드 예시**: 명확성을 위해 접을 수 있는 섹션을 사용하여 Python (및 다른 언어로 쉽게 확장 가능)에 대한 언어별 코드 블록을 포함합니다.

<details>
<summary>Python</summary>

```python
# general_search 도구 사용 예시
from mcp import ClientSession, StdioServerParameters
from mcp.client.stdio import stdio_client

async def run_search():
    server_params = StdioServerParameters(
        command="python",
        args=["server.py"],
    )
    async with stdio_client(server_params) as (reader, writer):
        async with ClientSession(reader, writer) as session:
            await session.initialize()
            result = await session.call_tool("general_search", arguments={"query": "open source LLMs"})
            print(result)
```
</details>

클라이언트를 실행하기 전에 서버가 무엇을 하는지 이해하는 것이 도움이 됩니다. [`server.py`](./server.py) 파일은 SerpAPI와 통합하여 웹, 뉴스, 제품 검색 및 Q&A를 위한 도구를 노출하는 MCP 서버를 구현합니다. 들어오는 요청을 처리하고, API 호출을 관리하고, 응답을 구문 분석하고, 구조화된 결과를 클라이언트에 반환합니다.

[`server.py`](./server.py)에서 전체 구현을 검토할 수 있습니다.

다음은 서버가 도구를 정의하고 등록하는 방법에 대한 간략한 예시입니다:

<details>
<summary>Python 서버</summary>

```python
# server.py (발췌)
from mcp.server import MCPServer, Tool

async def general_search(query: str):
    # ...구현...

server = MCPServer()
server.add_tool(Tool("general_search", general_search))

if __name__ == "__main__":
    server.run()
```
</details>

- **외부 API 통합**: API 키 및 외부 요청의 안전한 처리를 보여줍니다.
- **구조화된 데이터 구문 분석**: API 응답을 LLM 친화적인 형식으로 변환하는 방법을 보여줍니다.
- **오류 처리**: 적절한 로깅을 통한 강력한 오류 처리
- **대화형 클라이언트**: 테스트를 위한 자동화된 테스트 및 대화형 모드 모두 포함
- **컨텍스트 관리**: 요청 로깅 및 추적을 위해 MCP 컨텍스트 활용

## 전제 조건

시작하기 전에 다음 단계를 따라 환경이 올바르게 설정되었는지 확인하십시오. 이렇게 하면 모든 종속성이 설치되고 API 키가 원활한 개발 및 테스트를 위해 올바르게 구성됩니다.

- Python 3.8 이상
- SerpAPI API 키 ([SerpAPI](https://serpapi.com/)에서 가입 - 무료 티어 사용 가능)

## 설치

시작하려면 다음 단계를 따라 환경을 설정하십시오:

1. uv (권장) 또는 pip를 사용하여 종속성 설치:

```bash
# uv 사용 (권장)
uv pip install -r requirements.txt

# pip 사용
pip install -r requirements.txt
```

2. 프로젝트 루트에 SerpAPI 키가 포함된 `.env` 파일을 생성합니다:

```
SERPAPI_KEY=your_serpapi_key_here
```

## 사용법

웹 검색 MCP 서버는 SerpAPI와 통합하여 웹, 뉴스, 제품 검색 및 Q&A를 위한 도구를 노출하는 핵심 구성 요소입니다. 들어오는 요청을 처리하고, API 호출을 관리하고, 응답을 구문 분석하고, 구조화된 결과를 클라이언트에 반환합니다.

[`server.py`](./server.py)에서 전체 구현을 검토할 수 있습니다.

### 서버 실행

MCP 서버를 시작하려면 다음 명령을 사용하십시오:

```bash
python server.py
```

서버는 클라이언트가 직접 연결할 수 있는 stdio 기반 MCP 서버로 실행됩니다.

### 클라이언트 모드

클라이언트(`client.py`)는 MCP 서버와 상호 작용하기 위한 두 가지 모드를 지원합니다:

- **일반 모드**: 모든 도구를 실행하고 응답을 확인하는 자동화된 테스트를 실행합니다. 이는 서버와 도구가 예상대로 작동하는지 빠르게 확인하는 데 유용합니다.
- **대화형 모드**: 메뉴 기반 인터페이스를 시작하여 도구를 수동으로 선택하고 호출하고, 사용자 지정 쿼리를 입력하고, 실시간으로 결과를 볼 수 있습니다. 이는 서버의 기능을 탐색하고 다양한 입력으로 실험하는 데 이상적입니다.

[`client.py`](./client.py)에서 전체 구현을 검토할 수 있습니다.

### 클라이언트 실행

자동화된 테스트를 실행하려면 (서버가 자동으로 시작됩니다):

```bash
python client.py
```

또는 대화형 모드로 실행:

```bash
python client.py --interactive
```

### 다양한 방법으로 테스트

요구 사항 및 워크플로우에 따라 서버에서 제공하는 도구를 테스트하고 상호 작용하는 여러 가지 방법이 있습니다.

#### MCP Python SDK로 사용자 지정 테스트 스크립트 작성
MCP Python SDK를 사용하여 자신만의 테스트 스크립트를 구축할 수도 있습니다:

<details>
<summary>Python</summary>

```python
from mcp import ClientSession, StdioServerParameters
from mcp.client.stdio import stdio_client

async def test_custom_query():
    server_params = StdioServerParameters(
        command="python",
        args=["server.py"],
    )
    
    async with stdio_client(server_params) as (reader, writer):
        async with ClientSession(reader, writer) as session:
            await session.initialize()
            # 사용자 지정 매개변수로 도구 호출
            result = await session.call_tool("general_search", 
                                           arguments={"query": "your custom query"})
            # 결과 처리
```
</details>


이 컨텍스트에서 "테스트 스크립트"는 MCP 서버의 클라이언트 역할을 하도록 작성하는 사용자 지정 Python 프로그램을 의미합니다. 공식적인 단위 테스트 대신, 이 스크립트를 사용하면 서버에 프로그래밍 방식으로 연결하고, 선택한 매개변수로 모든 도구를 호출하고, 결과를 검사할 수 있습니다. 이 접근 방식은 다음을 위해 유용합니다:
- 도구 호출 프로토타입 제작 및 실험
- 서버가 다양한 입력에 어떻게 응답하는지 유효성 검사
- 반복적인 도구 호출 자동화
- MCP 서버 위에 자신만의 워크플로우 또는 통합 구축

테스트 스크립트를 사용하여 새로운 쿼리를 빠르게 시도하고, 도구 동작을 디버그하거나, 더 고급 자동화를 위한 시작점으로 사용할 수 있습니다. 다음은 MCP Python SDK를 사용하여 이러한 스크립트를 만드는 방법에 대한 예시입니다:

## 도구 설명

서버에서 제공하는 다음 도구를 사용하여 다양한 유형의 검색 및 쿼리를 수행할 수 있습니다. 각 도구는 매개변수 및 사용 예시와 함께 아래에 설명되어 있습니다.


이 섹션에서는 사용 가능한 각 도구 및 해당 매개변수에 대한 세부 정보를 제공합니다.

### general_search

일반 웹 검색을 수행하고 형식화된 결과를 반환합니다.

**이 도구를 호출하는 방법:**

MCP Python SDK를 사용하여 자신만의 스크립트에서 `general_search`를 호출하거나, Inspector 또는 대화형 클라이언트 모드를 사용하여 대화식으로 호출할 수 있습니다. 다음은 SDK를 사용하는 코드 예시입니다:

<details>
<summary>Python 예시</summary>

```python
from mcp import ClientSession, StdioServerParameters
from mcp.client.stdio import stdio_client

async def run_general_search():
    server_params = StdioServerParameters(
        command="python",
        args=["server.py"],
    )
    async with stdio_client(server_params) as (reader, writer):
        async with ClientSession(reader, writer) as session:
            await session.initialize()
            result = await session.call_tool("general_search", arguments={"query": "latest AI trends"})
            print(result)
```
</details>

또는 대화형 모드에서 메뉴에서 `general_search`를 선택하고 프롬프트가 표시되면 쿼리를 입력합니다.

**매개변수:**
- `query` (문자열): 검색 쿼리

**요청 예시:**

```json
{
  "query": "latest AI trends"
}
```

### news_search

쿼리와 관련된 최신 뉴스 기사를 검색합니다.

**이 도구를 호출하는 방법:**

MCP Python SDK를 사용하여 자신만의 스크립트에서 `news_search`를 호출하거나, Inspector 또는 대화형 클라이언트 모드를 사용하여 대화식으로 호출할 수 있습니다. 다음은 SDK를 사용하는 코드 예시입니다:

<details>
<summary>Python 예시</summary>

```python
from mcp import ClientSession, StdioServerParameters
from mcp.client.stdio import stdio_client

async def run_news_search():
    server_params = StdioServerParameters(
        command="python",
        args=["server.py"],
    )
    async with stdio_client(server_params) as (reader, writer):
        async with ClientSession(reader, writer) as session:
            await session.initialize()
            result = await session.call_tool("news_search", arguments={"query": "AI policy updates"})
            print(result)
```
</details>

또는 대화형 모드에서 메뉴에서 `news_search`를 선택하고 프롬프트가 표시되면 쿼리를 입력합니다.

**매개변수:**
- `query` (문자열): 검색 쿼리

**요청 예시:**

```json
{
  "query": "AI policy updates"
}
```

### product_search

쿼리와 일치하는 제품을 검색합니다.

**이 도구를 호출하는 방법:**

MCP Python SDK를 사용하여 자신만의 스크립트에서 `product_search`를 호출하거나, Inspector 또는 대화형 클라이언트 모드를 사용하여 대화식으로 호출할 수 있습니다. 다음은 SDK를 사용하는 코드 예시입니다:

<details>
<summary>Python 예시</summary>

```python
from mcp import ClientSession, StdioServerParameters
from mcp.client.stdio import stdio_client

async def run_product_search():
    server_params = StdioServerParameters(
        command="python",
        args=["server.py"],
    )
    async with stdio_client(server_params) as (reader, writer):
        async with ClientSession(reader, writer) as session:
            await session.initialize()
            result = await session.call_tool("product_search", arguments={"query": "best AI gadgets 2025"})
            print(result)
```
</details>

또는 대화형 모드에서 메뉴에서 `product_search`를 선택하고 프롬프트가 표시되면 쿼리를 입력합니다.

**매개변수:**
- `query` (문자열): 제품 검색 쿼리

**요청 예시:**

```json
{
  "query": "best AI gadgets 2025"
}
```

### qna

검색 엔진에서 질문에 대한 직접적인 답변을 가져옵니다.

**이 도구를 호출하는 방법:**

MCP Python SDK를 사용하여 자신만의 스크립트에서 `qna`를 호출하거나, Inspector 또는 대화형 클라이언트 모드를 사용하여 대화식으로 호출할 수 있습니다. 다음은 SDK를 사용하는 코드 예시입니다:

<details>
<summary>Python 예시</summary>

```python
from mcp import ClientSession, StdioServerParameters
from mcp.client.stdio import stdio_client

async def run_qna():
    server_params = StdioServerParameters(
        command="python",
        args=["server.py"],
    )
    async with stdio_client(server_params) as (reader, writer):
        async with ClientSession(reader, writer) as session:
            await session.initialize()
            result = await session.call_tool("qna", arguments={"question": "what is artificial intelligence"})
            print(result)
```
</details>

또는 대화형 모드에서 메뉴에서 `qna`를 선택하고 프롬프트가 표시되면 질문을 입력합니다.

**매개변수:**
- `question` (문자열): 답변을 찾을 질문

**요청 예시:**

```json
{
  "question": "what is artificial intelligence"
}
```

## 코드 세부 정보

이 섹션에서는 서버 및 클라이언트 구현을 위한 코드 스니펫 및 참조를 제공합니다.

<details>
<summary>Python</summary>

전체 구현 세부 정보는 [`server.py`](./server.py) 및 [`client.py`](./client.py)를 참조하십시오.

```python
# server.py의 예시 스니펫:
import os
import httpx
# ...기존 코드...
```
</details>

## 이 레슨의 고급 개념

구축을 시작하기 전에 이 장 전체에 나타날 몇 가지 중요한 고급 개념이 있습니다. 이러한 개념을 이해하면 익숙하지 않더라도 따라가는 데 도움이 될 것입니다:

- **다중 도구 오케스트레이션**: 이는 단일 MCP 서버 내에서 여러 다른 도구(예: 웹 검색, 뉴스 검색, 제품 검색 및 Q&A)를 실행하는 것을 의미합니다. 이를 통해 서버는 단일 작업이 아닌 다양한 작업을 처리할 수 있습니다.
- **API 속도 제한 처리**: 많은 외부 API(예: SerpAPI)는 특정 시간 내에 만들 수 있는 요청 수를 제한합니다. 좋은 코드는 이러한 제한을 확인하고 우아하게 처리하여 제한에 도달하더라도 앱이 중단되지 않도록 합니다.
- **구조화된 데이터 구문 분석**: API 응답은 종종 복잡하고 중첩됩니다. 이 개념은 이러한 응답을 LLM 또는 다른 프로그램에 친숙한 깨끗하고 사용하기 쉬운 형식으로 변환하는 것입니다.
- **오류 복구**: 때로는 네트워크 오류가 발생하거나 API가 예상한 것을 반환하지 않는 등 문제가 발생할 수 있습니다. 오류 복구는 코드가 이러한 문제를 처리하고 충돌하지 않고도 유용한 피드백을 제공할 수 있음을 의미합니다.
- **매개변수 유효성 검사**: 이는 도구에 대한 모든 입력이 올바르고 안전하게 사용될 수 있는지 확인하는 것입니다. 기본값을 설정하고 유형이 올바른지 확인하는 것을 포함하며, 이는 버그 및 혼란을 방지하는 데 도움이 됩니다.

이 섹션은 웹 검색 MCP 서버 작업 중에 발생할 수 있는 일반적인 문제를 진단하고 해결하는 데 도움이 될 것입니다. 웹 검색 MCP 서버 작업 중에 오류 또는 예기치 않은 동작이 발생하면 이 문제 해결 섹션에서 가장 일반적인 문제에 대한 해결책을 제공합니다. 추가 도움을 요청하기 전에 이 팁을 검토하십시오. 종종 문제를 빠르게 해결할 수 있습니다.

## 문제 해결

웹 검색 MCP 서버로 작업할 때 때때로 문제가 발생할 수 있습니다. 이는 외부 API 및 새로운 도구로 개발할 때 정상적인 현상입니다. 이 섹션에서는 가장 일반적인 문제에 대한 실용적인 해결책을 제공하므로 빠르게 다시 시작할 수 있습니다. 오류가 발생하면 여기에서 시작하십시오. 아래 팁은 대부분의 사용자가 직면하는 문제를 해결하고 추가 도움 없이 문제를 해결할 수 있습니다.

### 일반적인 문제

다음은 사용자가 가장 자주 겪는 몇 가지 문제와 명확한 설명 및 해결 단계입니다:

1. **.env 파일에 SERPAPI_KEY 없음**
   - `SERPAPI_KEY 환경 변수를 찾을 수 없습니다` 오류가 표시되면 애플리케이션이 SerpAPI에 액세스하는 데 필요한 API 키를 찾을 수 없다는 의미입니다. 이 문제를 해결하려면 프로젝트 루트에 `.env`라는 파일을 생성(아직 없는 경우)하고 `SERPAPI_KEY=your_serpapi_key_here`와 같은 줄을 추가하십시오. `your_serpapi_key_here`를 SerpAPI 웹사이트의 실제 키로 바꾸십시오.

2. **모듈을 찾을 수 없음 오류**
   - `ModuleNotFoundError: 'httpx'라는 모듈을 찾을 수 없습니다`와 같은 오류는 필요한 Python 패키지가 없음을 나타냅니다. 이는 일반적으로 모든 종속성을 설치하지 않은 경우에 발생합니다. 이 문제를 해결하려면 터미널에서 `pip install -r requirements.txt`를 실행하여 프로젝트에 필요한 모든 것을 설치하십시오.

3. **연결 문제**
   - `클라이언트 실행 중 오류`와 같은 오류가 발생하면 클라이언트가 서버에 연결할 수 없거나 서버가 예상대로 실행되지 않는다는 의미입니다. 클라이언트와 서버가 호환되는 버전인지, `server.py`가 올바른 디렉토리에 있고 실행 중인지 다시 확인하십시오. 서버와 클라이언트 모두를 다시 시작하는 것도 도움이 될 수 있습니다.

4. **SerpAPI 오류**
   - `검색 API가 오류 상태: 401을 반환했습니다`가 표시되면 SerpAPI 키가 없거나, 잘못되었거나, 만료되었다는 의미입니다. SerpAPI 대시보드로 이동하여 키를 확인하고 필요한 경우 `.env` 파일을 업데이트하십시오. 키가 올바른데도 이 오류가 계속 표시되면 무료 티어 할당량이 소진되었는지 확인하십시오.

### 디버그 모드

기본적으로 앱은 중요한 정보만 로깅합니다. 무슨 일이 일어나고 있는지에 대한 자세한 내용을 보려면(예: 까다로운 문제 진단), 디버그 모드를 활성화할 수 있습니다. 이렇게 하면 앱이 수행하는 각 단계에 대한 훨씬 더 많은 정보를 볼 수 있습니다.

**예시: 일반 출력**
```plaintext
2025-06-01 10:15:23,456 - __main__ - INFO - Calling general_search with params: {'query': 'open source LLMs'}
2025-06-01 10:15:24,123 - __main__ - INFO - Successfully called general_search

GENERAL_SEARCH RESULTS:
... (검색 결과 여기) ...
```

**예시: 디버그 출력**
```plaintext
2025-06-01 10:15:23,456 - __main__ - INFO - Calling general_search with params: {'query': 'open source LLMs'}
2025-06-01 10:15:23,457 - httpx - DEBUG - HTTP Request: GET https://serpapi.com/search ...
2025-06-01 10:15:23,458 - httpx - DEBUG - HTTP Response: 200 OK ...
2025-06-01 10:15:24,123 - __main__ - INFO - Successfully called general_search

GENERAL_SEARCH RESULTS:
... (검색 결과 여기) ...
```

디버그 모드에는 HTTP 요청, 응답 및 기타 내부 세부 정보에 대한 추가 줄이 포함되어 있습니다. 이는 문제 해결에 매우 유용할 수 있습니다.

디버그 모드를 활성화하려면 `client.py` 또는 `server.py` 상단에서 로깅 수준을 DEBUG로 설정하십시오:

<details>
<summary>Python</summary>

```python
# client.py 또는 server.py 상단
import logging
logging.basicConfig(
    level=logging.DEBUG,  # INFO에서 DEBUG로 변경
    format="%(asctime)s - %(name)s - %(levelname)s - %(message)s"
)
```
</details>

---

## 다음 단계

- [5.10 실시간 스트리밍](../mcp-realtimestreaming/README.md)
