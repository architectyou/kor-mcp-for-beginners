# 이 샘플 실행

> [!NOTE]
> 이 샘플은 GitHub Codespaces 인스턴스를 사용한다고 가정합니다. 로컬에서 실행하려면 GitHub에서 개인용 액세스 토큰(PAT)을 설정해야 합니다.
>
> ```bash
> # zsh/bash
> export GITHUB_TOKEN="{{YOUR_GITHUB_PAT}}"
> ```
>
> ```powershell
> # PowerShell
> $env:GITHUB_TOKEN = "{{YOUR_GITHUB_PAT}}"
> ```

## 라이브러리 설치

```sh
dotnet restore
```

다음 라이브러리가 설치되어야 합니다: Azure AI Inference, Azure Identity, Microsoft.Extension, Model.Hosting, ModelContextProtcol

## 실행

```sh 
dotnet run
```

다음과 유사한 출력이 표시되어야 합니다.

```text
stdio 전송 설정 중
도구 나열
도구가 있는 서버에 연결됨: 추가
도구 설명: 두 숫자를 더합니다
도구 매개변수: {"title":"Add","description":"두 숫자를 더합니다","type":"object","properties":{"a":{"type":"integer"},"b":{"type":"integer"}},"required":["a","b"]}
도구 정의: Azure.AI.Inference.ChatCompletionsToolDefinition
속성: {"a":{"type":"integer"},"b":{"type":"integer"}}
MCP 도구 정의: 0: Azure.AI.Inference.ChatCompletionsToolDefinition
도구 호출 0: 인수가 있는 추가 {"a":2,"b":4}
합계 6
```

출력의 대부분은 디버깅이지만 중요한 것은 MCP 서버에서 도구를 나열하고, 이를 LLM 도구로 변환하고, 최종적으로 MCP 클라이언트 응답 "합계 6"을 얻는다는 것입니다.

