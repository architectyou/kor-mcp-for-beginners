# 이 샘플 실행하기

`uv`를 설치하는 것이 좋지만 필수는 아닙니다. [지침](https://docs.astral.sh/uv/#highlights)을 참조하세요.

## -0- 가상 환경 만들기

```bash
python -m venv venv
```

## -1- 가상 환경 활성화

```bash
venc\Scrips\activate
```

## -2- 종속성 설치

```bash
pip install "mcp[cli]"
pip install openai
pip install azure-ai-inference
```

## -3- 샘플 실행


```bash
python client.py
```

다음과 유사한 출력이 표시되어야 합니다.

```text
리소스 나열
리소스: ('meta', None)
리소스: ('nextCursor', None)
리소스: ('resources', [])
                    INFO     ListToolsRequest 유형의 요청 처리                                                                               server.py:534
도구 나열
도구: add
도구 {'a': {'title': 'A', 'type': 'integer'}, 'b': {'title': 'B', 'type': 'integer'}}
LLM 호출 중
도구: {'function': {'arguments': '{"a":2,"b":20}', 'name': 'add'}, 'id': 'call_BCbyoCcMgq0jDwR8AuAF9QY3', 'type': 'function'}
[05/08/25 21:04:55] INFO     CallToolRequest 유형의 요청 처리                                                                                server.py:534
도구 결과: [TextContent(type='text', text='22', annotations=None)]
```