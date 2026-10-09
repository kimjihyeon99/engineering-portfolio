# Engineering Portfolio

**Enterprise AI · Backend · Full-stack Engineering**

Java / Spring Boot / React / TypeScript / Multi-LLM Integration

기업용 서비스에서 백엔드·프론트엔드 개발과 외부 시스템 연동을 수행했습니다. 문제의 원인을 분석하고, 설계상의 선택과 제약을 설명할 수 있는 구현을 지향합니다.

## Selected Engineering Cases

| 프로젝트 | 핵심 문제 | 직접 기여 및 확인된 결과 |
| --- | --- | --- |
| [Enterprise GenAI Platform](projects/enterprise-genai-platform.md) | 서로 다른 AI Provider의 호출·응답 통합과 기업용 모델 관리 | Multi-LLM 아키텍처 설계, Gemini 연동 검증·시연, 라우팅·DTO·Flux 스트리밍·모델 관리·이미지 생성 사용량 제한 구현 |
| [Offline-first Operations System](projects/offline-operations-system.md) | 조회 지연과 연결 제한 환경에서의 데이터 동기화 | 특정 조회 API **약 12초 → 3초 이하** (STG 직접 측정); Local-first UI, Bluetooth 기반 증분 동기화 |
| [Education Data Integration](projects/education-data-integration.md) | 외부 API 장애·지연이 사용자 조회에 미치는 영향 | Spring Batch 사전 적재, RDB 우선 조회, 트랜잭션 기반 데이터 교체 및 실패 시 기존 데이터 유지 |

## Core Strengths

- **Enterprise AI:** Provider별 모델 호출 추상화, 공통 응답 변환, Reactor Flux 스트리밍, 모델 등록·버전 관리
- **Backend Reliability:** 외부 API 의존성 분리, Timeout/Retry, Spring Batch, 트랜잭션 및 실패 복구
- **Full-stack Performance:** DB 인덱스 최적화, React Query 캐시 갱신, Local-first 데이터 표시
- **Offline Data Consistency:** Master–Slave 릴레이, 변경 레코드 단위 전송, 업데이트 시각 비교, Local DB 원자적 반영

## Technical Skills

프로젝트별 실제 사용 기술은 각 상세 문서에 따로 표기했습니다. 아래는 대표 경험 중심이며, 모든 기술의 숙련도가 동일하다는 의미는 아닙니다.

- **Backend:** Java, Spring Boot, Spring Batch, Spring Cloud OpenFeign, Project Reactor, MySQL, PostgreSQL
- **Frontend:** React, TypeScript, TanStack Query
- **AI & Cloud:** Gemini, GCP Vertex AI, Azure OpenAI, Provider SDK / REST API
- **Data & Operations:** MySQL, PostgreSQL, Local DB, Redis, Docker, Kubernetes

## Engineering Approach

1. **Separate failure domains:** 외부 시스템 호출과 사용자 요청 처리의 결합을 줄입니다.
2. **Make consistency rules explicit:** 데이터 갱신·충돌 해결·실패 시 동작을 구분합니다.
3. **Measure narrowly, describe accurately:** 측정한 API와 환경에만 수치를 적용합니다.
4. **State trade-offs:** 데이터 최신성, 응답성, 복구 가능성 사이의 선택을 설명합니다.

## Project Details

- [Enterprise GenAI Platform — Architecture & Implementation](projects/enterprise-genai-platform.md)
- [Offline-first Operations System — Performance & Synchronization](projects/offline-operations-system.md)
- [Education Data Integration — Batch & Reliability](projects/education-data-integration.md)
