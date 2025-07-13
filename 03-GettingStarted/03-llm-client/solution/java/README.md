# 계산기 LLM 클라이언트

LangChain4j를 사용하여 MCP(모델 컨텍스트 프로토콜) 계산기 서비스와 GitHub 모델 통합에 연결하는 방법을 보여주는 Java 애플리케이션입니다.

## 사전 요구 사항

- Java 21 이상
- Maven 3.6 이상(또는 포함된 Maven 래퍼 사용)
- GitHub 모델에 액세스할 수 있는 GitHub 계정
- `http://localhost:8080`에서 실행 중인 MCP 계산기 서비스

## GitHub 토큰 가져오기

이 애플리케이션은 GitHub 개인용 액세스 토큰이 필요한 GitHub 모델을 사용합니다. 토큰을 가져오려면 다음 단계를 따르십시오.

### 1. GitHub 모델 액세스
1. [GitHub 모델](https://github.com/marketplace/models)로 이동합니다.
2. GitHub 계정으로 로그인합니다.
3. 아직 액세스하지 않은 경우 GitHub 모델에 대한 액세스를 요청합니다.

### 2. 개인용 액세스 토큰 만들기
1. [GitHub 설정 → 개발자 설정 → 개인용 액세스 토큰 → 토큰(클래식)](https://github.com/settings/tokens)으로 이동합니다.
2. "새 토큰 생성" → "새 토큰 생성(클래식)"을 클릭합니다.
3. 토큰에 설명적인 이름(예: "MCP 계산기 클라이언트")을 지정합니다.
4. 필요에 따라 만료를 설정합니다.
5. 다음 범위를 선택합니다.
   - `repo`(개인 리포지토리에 액세스하는 경우)
   - `user:email`
6. "토큰 생성"을 클릭합니다.
7. **중요**: 토큰을 즉시 복사하십시오. 다시 볼 수 없습니다!

### 3. 환경 변수 설정

#### Windows(명령 프롬프트):
```cmd
set GITHUB_TOKEN=your_github_token_here
```

#### Windows(PowerShell):
```powershell
$env:GITHUB_TOKEN="your_github_token_here"
```

#### macOS/Linux:
```bash
export GITHUB_TOKEN=your_github_token_here
```

## 설정 및 설치

1. **프로젝트 디렉토리 복제 또는 탐색**

2. **필요한 종속성 설치**:
   ```cmd
   mvnw clean install
   ```
   또는 Maven이 전역적으로 설치된 경우:
   ```cmd
   mvn clean install
   ```

3. **환경 변수 설정**(위의 "GitHub 토큰 가져오기" 섹션 참조)

4. **MCP 계산기 서비스 시작**:
   1장의 MCP 계산기 서비스가 `http://localhost:8080`에서 실행 중인지 확인하십시오. 클라이언트를 시작하기 전에 실행 중이어야 합니다.

## 애플리케이션 실행

```cmd
mvnw clean package
java -jar target\calculator-llm-client-0.0.1-SNAPSHOT.jar
```

## 애플리케이션이 하는 일

애플리케이션을 실행하면 다음을 수행합니다.

1. **계산기 서비스에 연결**: `http://localhost:8080`
2. **도구 나열** - 사용 가능한 모든 계산기 작업을 표시합니다.
3. **계산 수행**:
   - 덧셈: 24.5 + 17.3
   - 제곱근: 144의 제곱근
   - 도움말: 사용 가능한 계산기 함수를 표시합니다.

## 예상 출력

성공적으로 실행되면 다음과 유사한 출력이 표시되어야 합니다.

```
24.5와 17.3의 합은 41.8입니다.
144의 제곱근은 12입니다.
계산기 서비스는 다음 함수를 제공합니다: add, subtract, multiply, divide, sqrt, power...
```

## 문제 해결

### 일반적인 문제

1. **"GITHUB_TOKEN 환경 변수가 설정되지 않았습니다"**
   - `GITHUB_TOKEN` 환경 변수를 설정했는지 확인하십시오.
   - 변수를 설정한 후 터미널/명령 프롬프트를 다시 시작하십시오.

2. **"localhost:8080에 대한 연결 거부됨"**
   - MCP 계산기 서비스가 포트 8080에서 실행 중인지 확인하십시오.
   - 다른 서비스가 포트 8080을 사용하고 있는지 확인하십시오.

3. **"인증 실패"**
   - GitHub 토큰이 유효하고 올바른 권한을 가지고 있는지 확인하십시오.
   - GitHub 모델에 대한 액세스 권한이 있는지 확인하십시오.

4. **Maven 빌드 오류**
   - Java 21 이상을 사용하고 있는지 확인하십시오. `java -version`
   - 빌드를 정리해 보십시오. `mvnw clean`

### 디버깅

디버그 로깅을 활성화하려면 실행 시 다음 JVM 인수를 추가하십시오.
```cmd
java -Dlogging.level.dev.langchain4j=DEBUG -jar target\calculator-llm-client-0.0.1-SNAPSHOT.jar
```

## 구성

애플리케이션은 다음으로 구성됩니다.
- `gpt-4.1-nano` 모델을 사용하는 GitHub 모델
- `http://localhost:8080/sse`의 MCP 서비스에 연결
- 요청에 대한 60초 시간 초과 사용
- 디버깅을 위한 요청/응답 로깅 활성화

## 종속성

이 프로젝트에서 사용되는 주요 종속성은 다음과 같습니다.
- **LangChain4j**: AI 통합 및 도구 관리를 위해
- **LangChain4j MCP**: 모델 컨텍스트 프로토콜 지원을 위해
- **LangChain4j GitHub 모델**: GitHub 모델 통합을 위해
- **Spring Boot**: 애플리케이션 프레임워크 및 종속성 주입을 위해

## 라이선스

이 프로젝트는 Apache License 2.0에 따라 라이선스가 부여됩니다. 자세한 내용은 [LICENSE](LICENSE) 파일을 참조하십시오.
