# 항공 객실 업무 통합 시스템

**기간:** 2026.01–2026.09  
**역할:** Software Engineer / Full-stack, 기술 설계 및 검토  
**주요 기술:** Java, Spring Boot, React, TypeScript, PostgreSQL, Redis, SQLite, Bluetooth

## 프로젝트 개요
객실 승무원이 비행·승객·기내 서비스 정보를 조회하고 기내 업무를 처리하는 통합 시스템입니다. Mobile/Hybrid App과 운영자용 Admin, 협력사용 Partner 시스템을 함께 구축했습니다.

**네트워크가 제한되는 기내에서도 업무가 지속되어야 하기 때문에**, 서버와 Local DB, 단말 간 Bluetooth 동기화를 함께 고려해 설계했습니다.

서비스 오픈·운영 대상은 **8,000명 이상의 임직원**입니다.

## 담당 역할
- Full-stack 개발과 Application 구조·기술 설계 검토
- 기술 검토 및 요구사항 조율, 시스템 간 연동 방식 설계·조율
- **Kiro 기반 SA Orchestrator**로 Architecture·Infra·API·DB 관점의 설계 검토를 구조화하고, ChatGPT로 기술 자료와 검토 결과를 교차 검증·보완
- Native, Backend, Frontend, 연계 담당자와 통합 설계 및 테스트 협업

## 대표 사례 1. 특정 조회 API 성능 개선
기내식 탑재량과 갤리 정보 조회 API에서 **12초 이상의 응답 지연**이 발생했습니다. 구간별 확인 결과 MyBatis SQL 실행이 병목임을 확인했고, 조회 조건과 데이터 접근 패턴을 분석해 Index를 적용했습니다.

화면에서는 Local DB 데이터를 먼저 표시하고 서버 조회·Local DB 갱신을 뒤에서 수행한 뒤 React Query를 갱신하도록 구성했습니다.

**결과: 특정 조회 API 12초 이상 → 3초 이내**

## 대표 사례 2. Offline Bluetooth 데이터 정합성
Master와 Slave 단말에서 같은 업무 데이터를 수정할 수 있어 충돌 기준이 필요했습니다. 기내식 주문은 마지막 변경 상태가 현재 유효한 상태라는 업무 규칙을 확인하고 **UPDATE 시각 기반 LWW**를 선택했습니다.

- 변경된 레코드만 전송하는 증분 동기화
- 업무 Key / Unique Key 기반 데이터 식별
- 복수 DML을 Local DB Transaction으로 처리
- UPSERT를 통한 중복 수신 대응
- 재연결 시 마지막 동기화 시각 기준 누락 데이터 재전송
- 큰 Payload에 선택적 Chunking 적용
- **Optimistic UI**로 사용자 조작 직후 화면을 먼저 반영하고 후속 동기화 상태와 정합성을 맞추는 구조 적용

LWW는 구현과 운영이 단순한 대신 변경 이력 전체를 보존하지 않고 단말 시각에 영향을 받을 수 있다는 Trade-off가 있습니다.

## 대표 사례 3. Local DB 정리 정책 변경
Splash 시점에 모든 만료 데이터를 일괄 삭제하는 안은 데이터가 증가할수록 앱 시작 경로에 부하가 집중될 수 있다고 판단했습니다.

대안으로 **각 업무 화면 진입 시 해당 업무의 만료 데이터만 정리**하는 방식을 제안했고, 다른 SA·SE·Native 담당자 및 고객과 검토해 설계에 반영했습니다.

## 주요 시스템 연계
### TMS - 사내 메일 전송 시스템
요청 성공이 최종 발송 성공을 의미하지 않는 특성을 고려해 **전송 요청과 결과 확인을 분리하고 결과 확인을 비동기로 처리**했습니다.

### ERP - 사내 재무 시스템
기내식 주문 부족량 정보를 재무 시스템과 연계했습니다. **기존 DB 직접 연계 방식에서 REST API 기반 일배치 방식으로 전환**했습니다.

## 기타 구현
- 대량 **참고 자료(Reference)**와 일반 이미지·문서 다운로드 Queue 분리
- React Query 기반 Local-first 조회 및 캐시 갱신
- Master/Slave 재연결·재전송 처리
- Admin / Partner / Hybrid WebView 개발


## Architecture

- [Offline / Bluetooth 동기화 구조 (draw.io)](../diagrams/offline-sync-architecture.drawio)

## AI 활용 Engineering · Kiro SA Orchestrator
요구사항과 프로젝트 설계 기준을 Spec으로 구체화하고, Kiro에서 **SA Orchestrator**를 구성해 다각도의 Architecture Review를 수행했습니다.

- Architecture Reviewer: 구조·설계·SPOF
- Infra Reviewer: 배포·스케일·장애
- API Reviewer: 계약·보안·성능
- DB Reviewer: 스키마·쿼리·트랜잭션
- Risk Aggregator: Findings 취합·중복 제거·위험 레벨 분류·연관 관계 분석
- Final SA Opinion: **GO / CONDITIONAL GO / NO-GO**
- 최종 산출물: Architecture Review Report

ChatGPT는 설계·기술 자료와 Review 결과의 **교차 검증 및 보완**에 활용했습니다. AI 결과는 그대로 적용하지 않고 기존 Architecture와 요구사항을 기준으로 직접 검증·수정했습니다.

- [Kiro SA Orchestrator (draw.io)](../diagrams/sa-orchestrator.drawio)
