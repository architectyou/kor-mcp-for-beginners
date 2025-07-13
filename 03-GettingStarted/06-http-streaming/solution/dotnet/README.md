# 이 샘플 실행하기

## -1- 종속성 설치

```bash
dotnet restore
```

## -2- 샘플 실행

```bash
dotnet run
```

## -3- 샘플 테스트

아래를 실행하기 전에 별도의 터미널을 시작하십시오(서버가 여전히 실행 중인지 확인하십시오).

한 터미널에서 서버를 실행한 상태에서 다른 터미널을 열고 다음 명령을 실행합니다.

```bash
npx @modelcontextprotocol/inspector http://localhost:3001
```

이렇게 하면 샘플을 테스트할 수 있는 시각적 인터페이스가 있는 웹 서버가 시작됩니다.

> 전송 유형으로 **스트리밍 가능한 HTTP**가 선택되어 있고 URL이 `http://localhost:3001/mcp`인지 확인하십시오.

서버가 연결되면:

- 도구 목록을 나열하고 `add`를 실행해 보세요. 인수 2와 4를 사용하면 결과에서 6을 볼 수 있습니다.
- 리소스 및 리소스 템플릿으로 이동하여 "greeting"을 호출하고 이름을 입력하면 제공한 이름으로 인사말이 표시됩니다.

### CLI 모드에서 테스트하기

다음 명령을 실행하여 CLI 모드에서 직접 시작할 수 있습니다.

```bash 
npx @modelcontextprotocol/inspector --cli http://localhost:3001 --method tools/list
```

그러면 서버에서 사용 가능한 모든 도구가 나열됩니다. 다음과 같은 출력이 표시되어야 합니다.

```text
{
  "tools": [
    {
      "name": "AddNumbers",
      "description": "두 숫자를 더합니다.",
      "inputSchema": {
        "type": "object",
        "properties": {
          "a": {
            "description": "첫 번째 숫자",
            "type": "integer"
          },
          "b": {
            "description": "두 번째 숫자",
            "type": "integer"
          }
        },
        "title": "AddNumbers",
        "description": "두 숫자를 더합니다.",
        "required": [
          "a",
          "b"
        ]
      }
    }
  ]
}
```

도구 유형을 호출하려면 다음을 입력하십시오.

```bash
npx @modelcontextprotocol/inspector --cli http://localhost:3001 --method tools/call --tool-name AddNumbers --tool-arg a=1 --tool-arg b=2
```

다음과 같은 출력이 표시되어야 합니다.

```text
{
  "content": [
    {
      "type": "text",
      "text": "3"
    }
  ],
  "isError": false
}
```

> ![!TIP]
> 일반적으로 브라우저보다 ispector를 CLI 모드에서 실행하는 것이 훨씬 빠릅니다.
> ispector에 대한 자세한 내용은 [여기](https://github.com/modelcontextprotocol/inspector)에서 확인하세요.
