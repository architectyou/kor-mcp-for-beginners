# 이 샘플 실행하기

`uv`를 설치하는 것이 좋지만 필수는 아닙니다. [지침](https://docs.astral.sh/uv/#highlights)을 참조하세요.

## -0- 가상 환경 만들기

```bash
python -m venv venv
```

## -1- 가상 환경 활성화

```bash
venv\Scrips\activate
```

## -2- 종속성 설치

```bash
pip install "mcp[cli]"
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
리소스 읽기
                    INFO     ReadResourceRequest 유형의 요청 처리                                                                            server.py:534
도구 호출
                    INFO     CallToolRequest 유형의 요청 처리                                                                                server.py:534
[TextContent(type='text', text='8', annotations=None)]
```