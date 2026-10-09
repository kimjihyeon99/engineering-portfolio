# Offline-first Operations System

**영역:** 연결이 제한된 현장 업무용 통합 시스템  
**기간:** 2026.01–2026.09  
**주요 기술:** React, TypeScript, TanStack Query, Local DB, Bluetooth, SQL

## Case A — 조회 API 및 Local-first UI 개선

### Problem

특정 업무 데이터 조회 API가 약 12초의 응답 지연을 보였습니다. 화면 진입·재진입 시 API 응답과 Local DB 갱신 완료를 기다리면서 기존 저장 데이터를 빠르게 표시하지 못했고, 검색 조건 변경 중 이전 결과가 사라지는 문제가 있었습니다.

### My Contributions

- DB 인덱스 최적화로 **특정 조회 API의 응답 시간 약 12초 → 3초 이하** 개선
- **STG 환경에서 개발 도구로 직접 측정**하고, **운영 환경에서 성능 테스트 담당자와 별도 검증 협업**
- 화면 진입 시 기존 Local DB 데이터를 먼저 표시하는 Local-first 조회 흐름 구현
- API 조회와 Local DB 갱신을 백그라운드로 수행한 후 `queryClient.invalidateQueries()`로 데이터 재조회
- 검색 조건 변경 시 이전 결과를 유지하고, `isFetching`, 캐시 정책, 메모이제이션을 활용해 상태 표시 개선

### Result & Measurement Boundary

| 항목 | 확인된 사실 |
| --- | --- |
| 대상 | 특정 업무 데이터 조회 API |
| STG 직접 측정 | 약 12초 → 3초 이하 |
| 협업 | 운영 환경 성능 테스트 담당자와 개선 결과 검증 |
| 수치의 범위 | 특정 API에 한정; 전체 서비스 평균·P95 또는 운영 환경 동일 수치를 뜻하지 않음 |

Local-first UI의 체감 대기 시간 개선은 정량적으로 측정됐다고 주장하지 않습니다.

## Case B — Bluetooth Star Topology 동기화

### Problem

여러 단말이 오프라인에서 각각 Local DB를 수정할 수 있어 변경 전파, 충돌 해결, 재연결 이후 동기화가 필요했습니다.

### Architecture

```mermaid
flowchart LR
    A[Slave A / Local DB] <--> M[Master / Local DB]
    M <--> B[Slave B / Local DB]
    M <--> C[Slave C / Local DB]
```

- **Master와 Slave 모두 일반 데이터 수정 가능**합니다. 전체 시스템이 Single-Writer인 것은 아닙니다.
- 한 Slave의 변경을 Master가 자신의 Local DB에 반영한 뒤 다른 Slave에 전파합니다.
- 특정 일괄 저장 기능만 Master에서 수정 가능하고 Slave에서는 조회 전용입니다.

### My Contributions & Consistency Rules

- 변경 전후를 비교해 **변경된 레코드 전체**를 전송하고, 변경이 없으면 전송을 생략
- 같은 레코드가 반복 변경되면 전송 직전 최신 상태를 추출; 이벤트 이력 전체를 재생하는 구조는 아님
- Master·Slave에서 수정 동작마다 UPDATE 시각 관리
- 단말 간 동일 레코드 충돌 시 UPDATE 시각을 비교해 최신 시각 우선 적용 (Last-Write-Wins)
- Native Bridge를 통한 INSERT/UPDATE/DELETE 복수 DML을 **단일 Local DB 트랜잭션**으로 처리; 중복 데이터에 UPSERT 적용
- Master·Slave 양측에 Optimistic UI 적용; 실패 시 Local DB 재조회로 화면 보정
- Native가 마지막 동기화 시각을 관리하고, 재연결 시 놓친 변경을 다시 전송

### Trade-offs & Boundaries

- Master 중심 릴레이는 전달 경로를 단순화하지만 Master에 대한 의존성이 생깁니다.
- 시각 기반 충돌 해결은 단순하지만 단말 간 시계 오차와 동시각 충돌 처리까지 검증된 것은 아닙니다.
- 마지막 동기화 시각 기반 재전송은 누락 변경 복구를 지원하지만 ACK·체크포인트 갱신 규칙이 확인되지 않아 **무손실 전송을 보장한다고 주장하지 않습니다**.
- UPSERT만으로 오래된 데이터 덮어쓰기를 방지한다고 주장하지 않습니다.
- Bluetooth 전송량 감소율, 동기화 지연 시간 등의 수치는 확인되지 않았습니다.
