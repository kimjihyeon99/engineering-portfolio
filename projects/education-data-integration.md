# Education Data Integration

**영역:** AI 디지털교과서(AIDT) 교육 플랫폼의 외부 데이터 연동  
**기간:** 2024.03–2025.06  
**주요 기술:** Java, Spring Boot, Spring Batch, Spring Cloud OpenFeign, MySQL, Redis

## 프로젝트 개요

교사와 학생이 사용하는 AI 디지털교과서 플랫폼의 구축부터 서비스 오픈 이후 운영까지 참여함. 콘텐츠·과제·운영자 기능을 개발하고, 운영 단계에서 사용자 문의·장애·외부 API 변경·데이터 조회 성능·보안 점검에 대응함.

일부 화면의 데이터는 외부 공공 API에 의존했음. 외부 시스템의 지연이나 장애가 사용자 조회에 직접 영향을 주지 않도록 **Spring Batch로 데이터를 미리 수집하고 MySQL에서 조회**하는 구조를 설계함.

## 담당 역할과 기술

- **직접 설계·구현:** 외부 API 정기 수집, Feign 호출 오류 대응, MySQL 트랜잭션 기반 데이터 교체, 사용자 조회 API
- **개발·운영:** React/TypeScript 화면 및 관리자 기능, 외부 연계 오류 분석, 운영 이슈 개선
- **기술 스택:** React, TypeScript, React Admin, Zustand, Recoil / Java, Spring Boot, Spring Batch, MyBatis, Feign Client / MySQL, Redis / Naver Cloud, EFK, GitLab CI/CD, Swagger, CSAP, Sparrow

## 문제와 설계 판단

사용자 요청마다 외부 API를 호출하면 외부 시스템의 응답 지연이나 일시적 실패가 화면 조회에 직접 전파될 수 있었음. 사용자 조회와 외부 API 호출을 분리하고, 수집 실패 시에도 마지막으로 정상 적재된 데이터를 제공하는 방식이 필요했음.

### 구현 방식

![Spring Batch 수집 및 MySQL 데이터 복구](../diagrams/aidt-batch-reliability.svg)

[Draw.io 원본 편집](../diagrams/aidt-batch-reliability.drawio)

1. 매일 Spring Batch에서 **당월·익월 데이터**를 외부 API로 조회함.
2. 외부 API 호출에는 Feign Timeout과 **최대 3회 재시도**를 적용함. 재시도 대상은 **Feign API 호출**이며 Batch Job 전체를 세 번 실행하는 방식은 아님.
3. 외부 API 수집이 성공한 경우에만 MySQL에서 대상 기간 데이터를 삭제하고 새 데이터를 INSERT함.
4. 삭제와 INSERT는 **하나의 DB 트랜잭션**으로 처리함. 저장 실패 시 ROLLBACK해 기존 데이터를 보존함.
5. 사용자 조회 API는 외부 API를 직접 호출하지 않고 MySQL에 마지막으로 정상 적재된 데이터를 조회함.
6. 외부 API 수집에 실패하면 기존 데이터를 유지하고 다음 정기 배치에서 갱신을 다시 시도함.

## 운영과 결과

외부 연계 시스템의 무응답 문제를 분석할 때 EFK(Elasticsearch, Fluentd/Fluent Bit, Kibana) 기반 로그 환경을 활용해 호출 및 오류 로그를 확인함.

- 사용자 조회 경로에서 외부 API 응답 대기를 분리함.
- 수집·저장 실패 시 기존 MySQL 데이터를 보존하도록 구성함.
- 실시간 데이터 대신 마지막 정상 수집 데이터를 제공하므로 **최신성은 배치 주기에 종속됨.**
- 트랜잭션은 부분 갱신을 막지만, 외부 API가 반환한 데이터의 정확성까지 보장하지는 않음.
- 응답 시간 단축이나 장애율 개선의 정량 수치는 별도로 확인되지 않아 기재하지 않음.
