# Enterprise GenAI Platform

**기간:** 2025.07–2025.12  
**역할:** Software Engineer / SA 역할 병행  
**주요 기술:** Java, Spring Boot, React, TypeScript, Project Reactor, Vertex AI, Azure OpenAI

## 프로젝트 개요
기업 내부에서 GenAI 과제를 수행할 때 과제 등록, 결재, 클라우드 환경 생성, LLM 검증이 각각 분리된 시스템과 수작업 절차로 운영되고 있었습니다.

각각 분리되어 있던 **과제 신청 → 승인 → 환경 준비 → AI 모델 검증** 과정을 하나의 서비스 흐름으로 연결했습니다.

## 담당 역할
- Application 구조 검토, 기술 대안 제시, 기술 검토 및 요구사항 조율
- Provider별 Service 분리 구조 제안
- 공통 요청·응답 DTO와 Reactor Flux 스트리밍 구현
- 모델 등록·버전 관리 및 이미지 생성 사용량 제한 구현
- **12개 AI 모델 연동**
- AIND를 활용해 **설계 변경 시 관련 Layer·API·DB 영향 범위를 분석**하고, 구현 전 검토 및 코드 리뷰 보조에 활용

## 대표 의사결정 1. Provider별 Service 분리
Provider마다 SDK 지원 범위, 인증, 요청·응답 형식이 달랐습니다. 상위 업무 로직에 Provider별 차이를 직접 노출하지 않고 AiService에서 Provider와 서비스 유형을 기준으로 호출 경로를 결정하고, Provider Service에서 공통 DTO로 변환하도록 설계했습니다.

SDK를 지원하는 기능은 SDK를 우선 사용하고, 미지원 기능은 REST API로 연동했습니다. 스트리밍 응답은 Reactor Flux로 공통화했습니다.

### 선택 기준과 Trade-off
Provider가 계속 늘어나는 구조라면 Service와 변환 코드가 함께 증가하는 비용이 있습니다. 하지만 프로젝트 요구사항은 **Provider 자체의 지속적인 증가보다 기존 Provider 내 모델·버전 추가가 중심**이었습니다.

따라서 과도한 플러그인 구조보다 Provider 차이를 명확하게 격리하고 유지보수하기 쉬운 구조를 선택했습니다.

## 대표 의사결정 2. 모델과 버전 정보를 데이터로 관리
기존 Provider 기준으로 모델 버전 정보가 추가·변경되는 요구를 고려해 DB에서 다음 정보를 관리했습니다.

- Provider
- Model
- Version
- 활성 여부

기존 모델의 버전 변경은 데이터 변경으로 대응하고, 새로운 서비스 유형이 추가되는 경우에만 Backend 라우팅과 Provider Service 구현을 확장하도록 경계를 나눴습니다.

## 대표 구현 3. 12개 AI 모델 연동
Google, Anthropic, Meta, OpenAI 계열의 LLM과 이미지 생성 모델을 연동했습니다. 모델별 Region, 입력 형식, SDK·REST 차이, 응답 형태를 Provider Service에서 흡수하고 상위 서비스에는 공통 계약을 제공했습니다.

## 사용량 및 운영 정책
사용자별 이미지 생성 횟수를 일 단위로 제한하고 생성 이력을 DB에서 관리했습니다. 과제 승인 이후 클라우드 환경 생성 인터페이스와 연계해 업무 흐름을 연결했습니다.

## 결과
- **12개 AI 모델 연동**
- Provider별 구현 차이를 서비스 계층으로 격리
- 모델 버전 변경을 DB 정보 변경으로 대응할 수 있는 구조 구성
- 공통 DTO와 Flux 기반 스트리밍으로 상위 서비스의 모델별 분기 최소화


## Architecture

- [Multi-LLM 연동 구조 (draw.io)](../diagrams/multi-llm-architecture.drawio)
