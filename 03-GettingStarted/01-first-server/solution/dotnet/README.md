# 이 샘플 실행하기

## -1- 종속성 설치

```bash
dotnet restore
```

## -3- 샘플 실행


```bash
dotnet run
```

## -4- 샘플 테스트

한 터미널에서 서버를 실행한 상태에서 다른 터미널을 열고 다음 명령을 실행합니다.

```bash
npx @modelcontextprotocol/inspector dotnet run
```

이렇게 하면 샘플을 테스트할 수 있는 시각적 인터페이스가 있는 웹 서버가 시작됩니다.

서버가 연결되면:

- 도구 목록을 나열하고 `add`를 실행해 보세요. 인수 2와 4를 사용하면 결과에서 6을 볼 수 있습니다.
- 리소스 및 리소스 템플릿으로 이동하여 "greeting"을 호출하고 이름을 입력하면 제공한 이름으로 인사말이 표시됩니다.

### CLI 모드에서 테스트하기

다음 명령을 실행하여 CLI 모드에서 직접 시작할 수 있습니다.

```bash
npx @modelcontextprotocol/inspector --cli dotnet run --method tools/list
```

그러면 서버에서 사용 가능한 모든 도구가 나열됩니다. 다음과 같은 출력이 표시되어야 합니다.

```text
{
  "tools": [
    {
      "name": "Add",
      "description": "두 숫자를 더합니다",
      "inputSchema": {
        "type": "object",
        "properties": {
          "a": {
            "type": "integer"
          },
          "b": {
            "type": "integer"
          }
        },
        "title": "Add",
        "description": "두 숫자를 더합니다",
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
npx @modelcontextprotocol/inspector --cli dotnet run --method tools/call --tool-name Add --tool-arg a=1 --tool-arg b=2
```

다음과 같은 출력이 표시되어야 합니다.

```text
{
  "content": [
    {
      "type": "text",
      "text": "Sum 3"
    }
  ],
  "isError": false
}
```

> ![!TIP]
> 일반적으로 브라우저보다 CLI 모드에서 검사기를 실행하는 것이 훨씬 빠릅니다.
> 검사기에 대한 자세한 내용은 [여기](https://github.com/modelcontextprotocol/inspector)에서 확인하세요.