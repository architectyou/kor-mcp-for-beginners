# MCP 고급 주제

이 장에서는 Model Context Protocol(MCP) 구현의 고급 주제들을 다룹니다. 여기에는 멀티모달 통합, 확장성, 보안 모범 사례, 엔터프라이즈 통합 등이 포함됩니다. 이러한 주제들은 현대 AI 시스템의 요구를 충족하는 견고하고 프로덕션 준비가 된 MCP 애플리케이션을 구축하는 데 매우 중요합니다.

## 개요

이 레슨에서는 MCP 구현의 고급 개념을 탐구합니다. 멀티모달 통합, 확장성, 보안 모범 사례, 엔터프라이즈 통합에 중점을 둡니다. 이러한 주제들은 엔터프라이즈 환경에서 복잡한 요구사항을 처리할 수 있는 프로덕션급 MCP 애플리케이션을 구축하는 데 필수적입니다.

## 학습 목표

이 레슨을 마치면 다음을 할 수 있습니다:

- MCP 프레임워크 내에서 멀티모달 기능 구현
- 고수요 시나리오를 위한 확장 가능한 MCP 아키텍처 설계
- MCP의 보안 원칙에 맞는 보안 모범 사례 적용
- 엔터프라이즈 AI 시스템 및 프레임워크와 MCP 통합
- 프로덕션 환경에서 성능과 신뢰성 최적화

## 레슨 및 샘플 프로젝트

| 링크 | 제목 | 설명 |
|------|-------|-------------|
| [5.1 Azure 통합](./mcp-integration/README.md) | Azure와 통합 | Azure에서 MCP 서버를 통합하는 방법을 배웁니다 |
| [5.2 멀티모달 샘플](./mcp-multi-modality/README.md) | MCP 멀티모달 샘플 | 오디오, 이미지, 멀티모달 응답 샘플 |
| [5.3 MCP OAuth2 샘플](./mcp-oauth2-demo/) | MCP OAuth2 데모 | OAuth2를 MCP에서 인증 및 리소스 서버로 사용하는 최소 Spring Boot 앱. 보안 토큰 발급, 보호된 엔드포인트, Azure Container Apps 배포, API Management 통합을 보여줍니다. |
| [5.4 루트 컨텍스트](./mcp-root-contexts/README.md) | 루트 컨텍스트 | 루트 컨텍스트에 대해 배우고 구현 방법을 익힙니다 |
| [5.5 라우팅](./mcp-routing/README.md) | 라우팅 | 다양한 라우팅 유형을 배웁니다 |
| [5.6 샘플링](./mcp-sampling/README.md) | 샘플링 | 샘플링을 다루는 방법을 배웁니다 |
| [5.7 확장성](./mcp-scaling/README.md) | 확장성 | 확장성에 대해 배웁니다 |
| [5.8 보안](./mcp-security/README.md) | 보안 | MCP 서버를 안전하게 보호하세요 |
| [5.9 웹 검색 샘플](./web-search-mcp/README.md) | 웹 검색 MCP | SerpAPI와 연동된 Python MCP 서버 및 클라이언트로 실시간 웹, 뉴스, 상품 검색 및 Q&A를 지원합니다. 멀티툴 오케스트레이션, 외부 API 연동, 견고한 오류 처리를 보여줍니다. |
| [5.10 실시간 스트리밍](./mcp-realtimestreaming/README.md) | 스트리밍 | 실시간 데이터 스트리밍은 오늘날 데이터 중심 환경에서 즉각적인 정보 접근이 필수인 비즈니스와 애플리케이션에 매우 중요합니다.|
| [5.11 실시간 웹 검색](./mcp-realtimesearch/README.md) | 웹 검색 | MCP가 AI 모델, 검색 엔진, 애플리케이션 전반에서 컨텍스트 관리를 표준화하여 실시간 웹 검색을 어떻게 혁신하는지 배웁니다.|
| [5.12 Entra ID 인증](./mcp-security-entra/README.md) | Entra ID 인증 | Microsoft Entra ID는 강력한 클라우드 기반 ID 및 접근 관리 솔루션을 제공하여, 오직 인가된 사용자와 애플리케이션만 MCP 서버와 상호작용할 수 있도록 보장합니다.|
| [5.13 Azure AI Foundry 에이전트 통합](./mcp-foundry-agent-integration/README.md) | Azure AI Foundry 통합 | Model Context Protocol 서버를 Azure AI Foundry 에이전트와 통합하여, 표준화된 외부 데이터 소스 연결과 강력한 도구 오케스트레이션, 엔터프라이즈 AI 기능을 활용하는 방법을 배웁니다.|

## 추가 참고자료

최신 MCP 고급 주제 정보는 다음을 참고하세요:
- [MCP 공식 문서](https://modelcontextprotocol.io/)
- [MCP 명세](https://spec.modelcontextprotocol.io/)
- [GitHub 저장소](https://github.com/modelcontextprotocol)

## 핵심 요약

- 멀티모달 MCP 구현은 AI의 능력을 텍스트 처리 그 이상으로 확장합니다
- 확장성은 엔터프라이즈 배포에 필수이며, 수평/수직 확장으로 해결할 수 있습니다
- 포괄적인 보안 조치는 데이터 보호와 적절한 접근 제어를 보장합니다
- Azure OpenAI, Microsoft AI Foundry 등과의 엔터프라이즈 통합으로 MCP의 역량이 강화됩니다
- 고급 MCP 구현은 최적화된 아키텍처와 신중한 리소스 관리의 이점을 누릴 수 있습니다

## 실습

특정 사용 사례에 대한 엔터프라이즈급 MCP 구현을 설계해보세요:

1. 해당 사용 사례의 멀티모달 요구사항을 식별하세요
2. 민감한 데이터 보호를 위한 보안 통제 방안을 정리하세요
3. 다양한 부하를 처리할 수 있는 확장 가능한 아키텍처를 설계하세요
4. 엔터프라이즈 AI 시스템과의 통합 지점을 계획하세요
5. 잠재적 성능 병목과 완화 전략을 문서화하세요

## 추가 자료

- [Azure OpenAI 공식 문서](https://learn.microsoft.com/en-us/azure/ai-services/openai/)
- [Microsoft AI Foundry 공식 문서](https://learn.microsoft.com/en-us/ai-services/)

---

## 다음 단계

- [5.1 MCP 통합](./mcp-integration/README.md)
