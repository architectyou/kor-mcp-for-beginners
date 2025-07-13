# 이 샘플 실행하기

클래식 HTTP 스트리밍 서버 및 클라이언트와 Python을 사용하는 MCP 스트리밍 서버 및 클라이언트를 실행하는 방법은 다음과 같습니다.

### 개요

- 항목을 처리할 때 진행률 알림을 클라이언트에 스트리밍하는 MCP 서버를 설정합니다.
- 클라이언트는 각 알림을 실시간으로 표시합니다.
- 이 가이드는 사전 요구 사항, 설정, 실행 및 문제 해결을 다룹니다.

### 사전 요구 사항

- Python 3.9 이상
- `mcp` Python 패키지(`pip install mcp`로 설치)

### 설치 및 설정

1. 리포지토리를 복제하거나 솔루션 파일을 다운로드합니다.

   ```pwsh
   git clone https://github.com/microsoft/mcp-for-beginners
   ```

1. **가상 환경 생성 및 활성화(권장):**

   ```pwsh
   python -m venv venv
   .\venv\Scripts\Activate.ps1  # Windows
   # 또는
   source venv/bin/activate      # Linux/macOS
   ```

1. **필요한 종속성 설치:**

   ```pwsh
   pip install "mcp[cli]"
   ```

### 파일

- **서버:** [server.py](./server.py)
- **클라이언트:** [client.py](./client.py)

### 클래식 HTTP 스트리밍 서버 실행

1. 솔루션 디렉토리로 이동합니다.

   ```pwsh
   cd 03-GettingStarted/06-http-streaming/solution
   ```

2. 클래식 HTTP 스트리밍 서버를 시작합니다.

   ```pwsh
   python server.py
   ```

3. 서버가 시작되고 다음이 표시됩니다.

   ```
   클래식 HTTP 스트리밍을 위한 FastAPI 서버 시작 중...
   INFO:     Uvicorn이 http://127.0.0.1:8000에서 실행 중입니다(CTRL+C를 눌러 종료).
   ```

### 클래식 HTTP 스트리밍 클라이언트 실행

1. 새 터미널을 엽니다(동일한 가상 환경 및 디렉토리 활성화).

   ```pwsh
   cd 03-GettingStarted/06-http-streaming/solution
   python client.py
   ```

2. 스트리밍된 메시지가 순차적으로 인쇄되는 것을 볼 수 있습니다.

   ```text
   클래식 HTTP 스트리밍 클라이언트 실행 중...
   메시지: hello로 http://localhost:8000/stream에 연결 중
   --- 스트리밍 진행률 ---
   파일 1/3 처리 중...
   파일 2/3 처리 중...
   파일 3/3 처리 중...
   파일 내용: hello
   --- 스트림 종료 ---
   ```

### MCP 스트리밍 서버 실행

1. 솔루션 디렉토리로 이동합니다.
   ```pwsh
   cd 03-GettingStarted/06-http-streaming/solution
   ```
2. streamable-http 전송으로 MCP 서버를 시작합니다.
   ```pwsh
   python server.py mcp
   ```
3. 서버가 시작되고 다음이 표시됩니다.
   ```
   streamable-http 전송으로 MCP 서버 시작 중...
   INFO:     Uvicorn이 http://127.0.0.1:8000에서 실행 중입니다(CTRL+C를 눌러 종료).
   ```

### MCP 스트리밍 클라이언트 실행

1. 새 터미널을 엽니다(동일한 가상 환경 및 디렉토리 활성화).
   ```pwsh
   cd 03-GettingStarted/06-http-streaming/solution
   python client.py mcp
   ```
2. 서버가 각 항목을 처리할 때 알림이 실시간으로 인쇄되는 것을 볼 수 있습니다.
   ```
   MCP 클라이언트 실행 중...
   클라이언트 시작 중...
   초기화 전 세션 ID: 없음
   초기화 후 세션 ID: a30ab7fca9c84f5fa8f5c54fe56c9612
   세션 초기화됨, 도구 호출 준비 완료.
   메시지 수신: root=LoggingMessageNotification(...)
   알림: root=LoggingMessageNotification(...)
   ...
   도구 결과: meta=None content=[TextContent(type='text', text='처리된 파일: file_1.txt, file_2.txt, file_3.txt | 메시지: 클라이언트에서 온 hello')]
   ```

### 주요 구현 단계

1. **FastMCP를 사용하여 MCP 서버를 만듭니다.**
2. **`ctx.info()` 또는 `ctx.log()`를 사용하여 목록을 처리하고 알림을 보내는 도구를 정의합니다.**
3. **`transport="streamable-http"`로 서버를 실행합니다.**
4. **알림이 도착하는 대로 표시하기 위한 메시지 처리기가 있는 클라이언트를 구현합니다.**

### 코드 워크스루
- 서버는 비동기 함수와 MCP 컨텍스트를 사용하여 진행률 업데이트를 보냅니다.
- 클라이언트는 비동기 메시지 처리기를 구현하여 알림과 최종 결과를 인쇄합니다.

### 팁 및 문제 해결

- 비차단 작업을 위해 `async/await`를 사용합니다.
- 견고성을 위해 서버와 클라이언트 모두에서 항상 예외를 처리합니다.
- 실시간 업데이트를 관찰하기 위해 여러 클라이언트로 테스트합니다.
- 오류가 발생하면 Python 버전을 확인하고 모든 종속성이 설치되었는지 확인하십시오.