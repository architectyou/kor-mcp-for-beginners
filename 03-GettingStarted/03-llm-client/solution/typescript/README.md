# 이 샘플 실행하기

이 샘플에는 클라이언트에 LLM이 포함되어 있습니다. LLM이 작동하려면 Codespaces에서 실행하거나 GitHub에서 개인용 액세스 토큰을 설정해야 합니다.

## -1- 종속성 설치

```bash
npm install
```

## -3- 서버 실행


```bash
npm run build
```

## -4- 클라이언트 실행

```sh
npm run client
```

다음과 유사한 결과가 표시되어야 합니다.

```text
서버에 사용 가능한 도구 요청 중
MCPClient started on stdin/stdout
LLM 쿼리 중: 2와 3의 합은 얼마입니까?
도구 호출 중
인수 {"a":2,"b":3}로 도구 add 호출 중
도구 결과: { content: [ { type: 'text', text: '5' } ] }
```