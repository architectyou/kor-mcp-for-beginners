# 샘플 실행

여기서는 이미 작동하는 서버 코드가 있다고 가정합니다. 이전 장의 서버를 찾으십시오.

## mcp.json 설정

참조용으로 사용할 파일은 [mcp.json](./mcp.json)입니다.

필요에 따라 서버 항목을 변경하여 서버의 절대 경로와 실행에 필요한 전체 명령을 가리키도록 합니다.

위에 언급된 예제 파일에서 서버 항목은 다음과 같습니다.

<details>
<summary>node.js</summary>
```json
"hello-mcp": {
    "command": "node",
    "args": [
        "build/index.js"
    ]
}
```
</details>

<details>
<summary>.NET</summary>

`git rev-parse --show-toplevel` 명령으로 가져올 수 있는 GitHub 리포지토리 루트를 입력해야 할 수 있습니다.

```jsonc
{
  "inputs": [
    {
      "type": "promptString",
      "id": "repository-root",
      "description": "리포지토리 루트의 절대 경로"
    }
  ],
  "servers": {
    "calculator-mcp-dotnet": {
      "type": "stdio",
      "command": "dotnet",
      "args": [
        "run",
        "--project",
        "${input:repository-root}/03-GettingStarted/02-client/solution/server/server.csproj"
      ]
    }
  }
}
```

</details>

이는 `node build/index.js`와 같은 명령을 실행하는 것과 일치합니다.

- 이 서버 항목을 변경하여 선택한 런타임 및 서버 위치에 따라 서버 파일이 있는 위치 또는 서버를 시작하는 데 필요한 항목에 맞게 조정합니다.

## 서버의 기능 사용

- *mcp.json*을 *./vscode* 폴더에 추가한 후 `재생` 아이콘을 클릭합니다.

    도구 아이콘이 사용 가능한 도구 수를 늘리는 것을 확인하십시오. 도구 아이콘은 GitHub Copilot의 채팅 필드 바로 위에 있습니다.

## 도구 실행

- 채팅 창에 도구 설명과 일치하는 프롬프트를 입력합니다. 예를 들어 `add` 도구를 트리거하려면 "3에 20을 더하세요"와 같은 것을 입력합니다.

    다음과 같이 도구를 실행하도록 선택하라는 메시지가 표시되는 채팅 텍스트 상자 위에 도구 아이콘이 표시됩니다.

    ![VS Code가 도구를 실행하려고 함을 나타냄](../assets/vscode-agent.png)

    이전에 언급한 것과 같은 프롬프트였다면 도구를 선택하면 "23"이라는 숫자 결과가 생성됩니다.