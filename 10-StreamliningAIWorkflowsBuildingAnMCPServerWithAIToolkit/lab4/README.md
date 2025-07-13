# 🐙 모듈 4: 실제 MCP 개발 - 사용자 지정 GitHub 클론 서버

![Duration](https://img.shields.io/badge/Duration-30_minutes-blue?style=flat-square)
![Difficulty](https://img.shields.io/badge/Difficulty-Intermediate-orange?style=flat-square)
![MCP](https://img.shields.io/badge/MCP-Custom%20Server-purple?style=flat-square&logo=github)
![VS Code](https://img.shields.io/badge/VS%20Code-Integration-blue?style=flat-square&logo=visualstudiocode)
![GitHub Copilot](https://img.shields.io/badge/GitHub%20Copilot-Agent%20Mode-green?style=flat-square&logo=github)

> **⚡ 빠른 시작:** GitHub 리포지토리 클론 및 VS Code 통합을 자동화하는 프로덕션 준비 MCP 서버를 단 30분 만에 구축하세요!

## 🎯 학습 목표

이 랩을 마치면 다음을 수행할 수 있습니다:

- ✅ 실제 개발 워크플로우를 위한 사용자 지정 MCP 서버 생성
- ✅ MCP를 통한 GitHub 리포지토리 클론 기능 구현
- ✅ 사용자 지정 MCP 서버를 VS Code 및 Agent Builder와 통합
- ✅ 사용자 지정 MCP 도구와 함께 GitHub Copilot Agent 모드 사용
- ✅ 프로덕션 환경에서 사용자 지정 MCP 서버 테스트 및 배포

## 📋 전제 조건

- 랩 1-3 완료 (MCP 기본 사항 및 고급 개발)
- GitHub Copilot 구독 ([무료 가입 가능](https://github.com/github-copilot/signup))
- AI 툴킷 및 GitHub Copilot 확장이 설치된 VS Code
- Git CLI 설치 및 구성

## 🏗️ 프로젝트 개요

### **실제 개발 과제**
개발자로서 우리는 GitHub를 사용하여 리포지토리를 클론하고 VS Code 또는 VS Code Insiders에서 여는 경우가 많습니다. 이 수동 프로세스에는 다음이 포함됩니다:
1. 터미널/명령 프롬프트 열기
2. 원하는 디렉토리로 이동
3. `git clone` 명령 실행
4. 클론된 디렉토리에서 VS Code 열기

**우리의 MCP 솔루션은 이를 단일 지능형 명령으로 간소화합니다!**

### **만들게 될 것**
다음 기능을 제공하는 **GitHub 클론 MCP 서버**(`git_mcp_server`):

| 기능 | 설명 | 이점 |
|---------|-------------|---------|
| 🔄 **스마트 리포지토리 클론** | 유효성 검사를 통해 GitHub 리포지토리 클론 | 자동화된 오류 검사 |
| 📁 **지능형 디렉토리 관리** | 디렉토리 안전하게 확인 및 생성 | 덮어쓰기 방지 |
| 🚀 **크로스 플랫폼 VS Code 통합** | VS Code/Insiders에서 프로젝트 열기 | 원활한 워크플로우 전환 |
| 🛡️ **강력한 오류 처리** | 네트워크, 권한 및 경로 문제 처리 | 프로덕션 준비된 안정성 |

---

## 📖 단계별 구현

### 1단계: Agent Builder에서 GitHub 에이전트 생성

1. AI 툴킷 확장을 통해 **Agent Builder 실행**
2. 다음 구성으로 **새 에이전트 생성**:
   ```
   에이전트 이름: GitHubAgent
   ```

3. **사용자 지정 MCP 서버 초기화:**
   - **도구** → **도구 추가** → **MCP 서버**로 이동
   - **"새 MCP 서버 생성"** 선택
   - 최대 유연성을 위해 **Python 템플릿** 선택
   - **서버 이름:** `git_mcp_server`

### 2단계: GitHub Copilot Agent 모드 구성

1. VS Code에서 **GitHub Copilot 열기** (Ctrl/Cmd + Shift + P → "GitHub Copilot: 열기")
2. Copilot 인터페이스에서 **에이전트 모델 선택**
3. 향상된 추론 기능을 위해 **Claude 3.7 모델 선택**
4. 도구 액세스를 위해 **MCP 통합 활성화**

> **💡 전문가 팁:** Claude 3.7은 개발 워크플로우 및 오류 처리 패턴에 대한 뛰어난 이해를 제공합니다.

### 3단계: 핵심 MCP 서버 기능 구현

**GitHub Copilot Agent 모드와 함께 다음 상세 프롬프트 사용:**

```
다음 포괄적인 요구 사항을 가진 두 가지 MCP 도구를 생성합니다:

🔧 도구 A: clone_repository
요구 사항:
- 모든 GitHub 리포지토리를 지정된 로컬 폴더로 클론
- 성공적으로 클론된 프로젝트의 절대 경로 반환
- 포괄적인 유효성 검사 구현:
  ✓ 대상 디렉토리가 이미 존재하는지 확인 (존재하면 오류 반환)
  ✓ GitHub URL 형식 유효성 검사 (https://github.com/user/repo)
  ✓ git 명령 가용성 확인 (누락된 경우 설치 프롬프트)
  ✓ 네트워크 연결 문제 처리
  ✓ 모든 실패 시나리오에 대해 명확한 오류 메시지 제공

🚀 도구 B: open_in_vscode
요구 사항:
- 지정된 폴더를 VS Code 또는 VS Code Insiders에서 열기
- 크로스 플랫폼 호환성 (Windows/Linux/macOS)
- 직접 애플리케이션 실행 사용 (터미널 명령 아님)
- 사용 가능한 VS Code 설치 자동 감지
- VS Code가 설치되지 않은 경우 처리
- 사용자 친화적인 오류 메시지 제공

추가 요구 사항:
- MCP 1.9.3 모범 사례 준수
- 적절한 타입 힌트 및 문서 포함
- 디버깅 목적으로 로깅 구현
- 모든 매개변수에 대한 입력 유효성 검사 추가
- 포괄적인 오류 처리 포함
```

### 4단계: MCP 서버 테스트

#### 4a. Agent Builder에서 테스트

1. Agent Builder에 대한 **디버그 구성 실행**
2. **이 시스템 프롬프트로 에이전트 구성:**

```
SYSTEM_PROMPT:
당신은 지능적인 코딩 리포지토리 도우미입니다. 개발자가 GitHub 리포지토리를 효율적으로 클론하고 개발 환경을 설정하도록 돕습니다. 항상 작업에 대한 명확한 피드백을 제공하고 오류를 우아하게 처리합니다.
```

3. **현실적인 사용자 시나리오로 테스트:**

```
USER_PROMPT 예시:

시나리오: 기본 클론 및 열기
"{https://github.com/kinfey/GHCAgentWorkshop과 같은 GitHub 리포지토리 링크}를 {지정하는 전역 경로}에 클론한 다음, VS Code Insiders로 엽니다."
```

![Agent Builder Testing](../../images/10-StreamliningAIWorkflowsBuildingAnMCPServerWithAIToolkit/lab4/DebugAgent.png)

**예상 결과:**
- ✅ 경로 확인을 통한 성공적인 클론
- ✅ 자동 VS Code 실행
- ✅ 유효하지 않은 시나리오에 대한 명확한 오류 메시지
- ✅ 엣지 케이스의 적절한 처리

#### 4b. MCP Inspector에서 테스트


![MCP Inspector Testing](../../images/10-StreamliningAIWorkflowsBuildingAnMCPServerWithAIToolkit/lab4/DebugInspector.png)

---



**🎉 축하합니다!** 실제 개발 워크플로우 문제를 해결하는 실용적이고 프로덕션 준비된 MCP 서버를 성공적으로 만들었습니다. 사용자 지정 GitHub 클론 서버는 개발자 생산성을 자동화하고 향상시키는 MCP의 강력한 기능을 보여줍니다.

### 🏆 달성 완료:
- ✅ **MCP 개발자** - 사용자 지정 MCP 서버 생성
- ✅ **워크플로우 자동화자** - 개발 프로세스 간소화
- ✅ **통합 전문가** - 여러 개발 도구 연결
- ✅ **프로덕션 준비 완료** - 배포 가능한 솔루션 구축

---

## 🎓 워크숍 완료: 모델 컨텍스트 프로토콜과의 여정

**워크숍 참가자 여러분께,**

모델 컨텍스트 프로토콜 워크숍의 네 가지 모듈을 모두 완료하신 것을 축하드립니다! 기본적인 AI 툴킷 개념을 이해하는 것부터 실제 개발 문제를 해결하는 프로덕션 준비 MCP 서버를 구축하는 것까지 많은 발전을 이루셨습니다.

### 🚀 학습 경로 요약:

**[모듈 1](../lab1/README.md)**: AI 툴킷 기본 사항, 모델 테스트 및 첫 AI 에이전트 생성부터 시작했습니다.

**[모듈 2](../lab2/README.md)**: MCP 아키텍처를 배우고, Playwright MCP를 통합하고, 첫 브라우저 자동화 에이전트를 구축했습니다.

**[모듈 3](../lab3/README.md)**: 날씨 MCP 서버를 사용하여 사용자 지정 MCP 서버 개발로 나아가고 디버깅 도구를 마스터했습니다.

**[모듈 4](../lab4/README.md)**: 이제 모든 것을 적용하여 실용적인 GitHub 리포지토리 워크플로우 자동화 도구를 만들었습니다.

### 🌟 마스터한 내용:

- ✅ **AI 툴킷 생태계**: 모델, 에이전트 및 통합 패턴
- ✅ **MCP 아키텍처**: 클라이언트-서버 설계, 전송 프로토콜 및 보안
- ✅ **개발자 도구**: 플레이그라운드에서 Inspector, 프로덕션 배포까지
- ✅ **사용자 지정 개발**: 자신만의 MCP 서버 구축, 테스트 및 배포
- ✅ **실제 애플리케이션**: AI를 사용하여 실제 워크플로우 문제 해결

### 🔮 다음 단계:

1. **자신만의 MCP 서버 구축**: 이러한 기술을 적용하여 고유한 워크플로우 자동화
2. **MCP 커뮤니티 참여**: 자신의 창작물을 공유하고 다른 사람에게 배우기
3. **고급 통합 탐색**: MCP 서버를 엔터프라이즈 시스템에 연결
4. **오픈 소스 기여**: MCP 도구 및 문서 개선에 도움

기억하세요, 이 워크숍은 시작에 불과합니다. 모델 컨텍스트 프로토콜 생태계는 빠르게 발전하고 있으며, 이제 AI 기반 개발 도구의 선두에 설 준비가 되었습니다.

**학습에 참여하고 헌신해 주셔서 감사합니다!**

이 워크숍이 개발 여정에서 AI 도구를 구축하고 상호 작용하는 방식을 변화시킬 아이디어를 불러일으켰기를 바랍니다.

**즐거운 코딩 되세요!**

---


