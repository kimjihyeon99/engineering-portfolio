# Engineering Portfolio

**Software Engineer | Full-stack · Backend · Solution Architecture**

Software Engineer로 서비스의 설계·개발·운영 전 주기를 경험했습니다. 화면·API·데이터·시스템 연계를 하나의 흐름으로 보고, 실제 사용 환경과 업무 규칙을 기준으로 기술 대안을 검토해 설계와 구현에 반영해 왔습니다.

GenAI Platform 프로젝트부터는 SA 역할을 병행하며 **기술 검토, 요구사항 조율, Application 구조와 시스템 연동 방식의 설계·검토**까지 역할을 확장했습니다.

## Impact at a Glance
- **8,000명+**: 항공 객실 업무 시스템 서비스 오픈·운영 대상 규모
- **12초 이상 → 3초 이내**: 특정 조회 API의 데이터 접근 패턴 분석 및 Index 적용을 통한 응답 시간 개선
- **12개 AI 모델 연동**: 여러 LLM/이미지 생성 모델을 공통 구조에서 사용할 수 있도록 연동
- **AI 활용 Engineering / Spec-Driven Development**: 요구사항과 설계 기준을 Spec/Context로 구체화해 AI를 설계·구현·검증에 활용. 항공 프로젝트는 **Kiro SA Orchestrator + ChatGPT**, GenAI Platform은 **Cursor + Claude**로 교차 검증·보완

## Selected Engineering Cases
| 프로젝트 | 핵심 문제 | 대표 기술 의사결정 / 결과 |
| --- | --- | --- |
| [항공 객실 업무 통합 시스템](projects/offline-operations-system.md) | 기내 네트워크 제약, 단말 간 데이터 정합성, 조회 성능, 시스템 연계 | Local-first, Bluetooth 증분 동기화·LWW·재전송, Optimistic UI, DB Index 최적화, TMS·ERP 연계 |
| [Enterprise GenAI Platform](projects/enterprise-genai-platform.md) | Provider별 SDK·응답 차이와 모델 버전 관리 | Provider Service 분리, 공통 DTO·Flux 스트리밍, DB 기반 Provider/Model/Version 관리, **12개 AI 모델 연동** |
| [AI 디지털교과서(AIDT)](projects/education-data-integration.md) | 교육 서비스 개발과 외부 API 장애 시 데이터 안정성 | 콘텐츠·과제·운영자 기능 개발, Batch 사전 적재, 트랜잭션 기반 교체, 실패 시 기존 데이터 보존 |

## Engineering Decision Principles
1. 사용 환경과 업무 규칙을 먼저 확인합니다.
2. 대안을 비교하고 장애 범위·정합성·성능·운영 복잡도를 기준으로 선택합니다.
3. 측정 가능한 결과는 수치로 확인하고, 측정하지 않은 효과는 과장하지 않습니다.
4. 설계의 장점뿐 아니라 Trade-off와 한계도 함께 설명합니다.

## Technical Skills
- **Backend:** Java, Spring Boot, Spring Batch, MyBatis, Redis, MySQL, PostgreSQL, REST API
- **Frontend:** React, TypeScript, TanStack Query, Zustand, MUI
- **LLM Integration:** Project Reactor / Flux, Vertex AI, Google Gen AI SDK, Anthropic Java SDK, Azure OpenAI SDK, Provider SDK / REST
- **Integration:** Feign Client, Quartz, RabbitMQ, API Gateway, 이메일 전송 시스템(TMS), ERP 등 시스템 연계
- **Infra / Operations:** AWS, GCP, Azure, Naver Cloud, Docker, Kubernetes, GitLab CI/CD, EFK(Elasticsearch · Fluent Bit · Kibana)
- **AI-assisted Engineering:** Spec-Driven Development, Kiro SA Orchestrator, Cursor, ChatGPT·Claude 기반 교차 검증

## Personal Project · Support Triage Agent

Python으로 고객 지원 문의의 고정 Workflow와 bounded Agent 실행 흐름을 구현했습니다. Planner가 다음 Tool을 선택하고 실행 결과를 Observation으로 축적하며, 서버가 허용 도구·인자·접근 권한·종료 조건을 검증하도록 책임을 분리했습니다.

- Tool Validator에서 3개 읽기 전용 Tool의 허용 목록, 필수 인자, 타입, 추가 키를 검증
- Tool Dispatcher에서 요청 주문번호 일치와 주문 접근 권한을 확인한 뒤 서비스 실행
- 반복 Tool 호출 차단, 최대 실행 단계 제한, 근거 부족·외부 의존성 오류·고위험 환불의 검토 경로 구현
- Agent 실행 루프가 공통 Dispatcher에 위임하도록 구성

**검증 범위** — Mock/시뮬레이터 기반 개인 실습입니다. 실제 LLM 호출·고객 API 연동·배포는 후속 구현 범위입니다. 저장소에는 총 54개 테스트의 통과 기록이 있으며, 이 문서 업데이트에서는 테스트를 재실행하지 않았습니다.

구현 소스는 비공개 학습 저장소에서 관리합니다. 공유 시에는 공개 가능한 코드 범위와 접근 권한을 별도로 정리합니다.

## Professional Development

**AWS Training and Certification**

- **2026** — Building Advanced Agentic Systems on AWS
- **2025** — Generative AI Essentials on AWS
- **2025** — Agentic AI Foundations


## Project Details
- [항공 객실 업무 통합 시스템](projects/offline-operations-system.md)
- [Enterprise GenAI Platform](projects/enterprise-genai-platform.md)
- [AI 디지털교과서(AIDT)](projects/education-data-integration.md)
