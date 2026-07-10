---
name: company-event-prep-orchestrator
description: 회사 또는 팀 행사를 기본 규칙, 준비물, 역할 분담, 일정, 안전 점검, 행사 후 회고로 나눠 준비할 때 사용한다. 초기 준비뿐 아니라 재실행, 부분 수정, 특정 단계만 다시("일정만 다시", "안전 점검만 다시"), 이전 회고 반영, 행사 전날 재점검 요청에도 사용한다.
---

# Company Event Prep Orchestrator

## 목적

이 Orchestrator는 회사 행사 준비를 준비물/역할, 일정, 안전 점검, 행사 후 회고 네 갈래로 나누고, 중간 산출물을 이어서 최종 준비 상태를 만든다. 예산·외부계약·개인정보·안전 최종 확인은 사람 승인 없이 완료 처리하지 않는다.

## 실행 모드

기본 실행 모드는 소규모 Agent Team이다(팀원 4명: prep-coordinator, scheduler, safety-checker, retrospective-writer). safety-checker의 교차 검토와 보완 요청이 실시간 조율을 필요로 하므로 단일 흐름보다 팀 협업이 적합하다.

## 실행 모드 확인

1. `artifacts/`에 `00-event-params.md`가 있는지 확인한다.
2. 없으면 **초기 실행**으로 진행한다.
3. 있고 사용자가 "일정만 다시", "준비물만 보완해줘", "안전 점검만 다시"처럼 특정 단계만 요청하면 **부분 재실행**으로 해당 Task만 다시 수행한다.
4. 사용자가 완전히 새로운 행사를 준비하자고 하면, 기존 산출물을 `artifacts/archive/{YYYYMMDD-HHMMSS}/`로 옮기고 **새 실행**을 시작한다.
5. 사용자가 "지난번 회고 반영해서 다시"라고 하면 `05-retrospective.md`와 `improvement-log.md`를 먼저 읽고 그 내용을 이번 `00-event-params.md`·Task 지시에 반영한다.
6. 의도가 불분명하면 "이어 하기 / 부분 수정 / 새 행사로 새 실행" 중 무엇인지 먼저 확인한다.
7. 부분 재실행으로 앞 단계 산출물이 바뀌면, 그 산출물을 입력으로 삼는 뒤 단계 파일을 `artifacts/README.md`에서 `stale`로 표시한다.

## 0단계: 표준 규칙 확인

매 실행 시작 시 `references/standing-rules.md`를 읽는다. "확인 필요"로 남은 항목(예산 승인 절차, 안전 필수 항목, 복장 규정 등)이 있으면 사용자에게 한 번 확인하고, 답을 받으면 이 파일에 반영해 이후 실행부터 재사용한다. 이미 채워져 있으면 그대로 사용한다.

## 1단계: 탐색 — `artifacts/00-event-params.md`

행사 날짜, 예산, 인원, 장소(실내/야외), 목적을 확인한다. 사용자가 아직 안 준 값은 "확인 필요"로 남기고 질문한다. 이 파일이 다 채워지기 전에는 계획 단계(2단계)로 넘어가지 않는다.

## 2단계: 계획 (병렬)

| Task | 담당 | 입력 | 출력 | 의존 |
| --- | --- | --- | --- | --- |
| T01-params | Orchestrator | 사용자 입력 | `artifacts/00-event-params.md` | 없음 |
| T02-checklist | prep-coordinator | `standing-rules.md`, `00-event-params.md` | `artifacts/01-checklist-roles.md` | T01 |
| T03-schedule | scheduler | `00-event-params.md`, `01-checklist-roles.md` | `artifacts/02-schedule.md` | T01 (T02와 병렬 시작 가능, 단 일정 세부는 T02 완료 후 확정) |

`TeamCreate`로 4명을 팀원화하고, `TaskCreate`로 위 Task를 등록한다. 각 담당자는 시작·차단·완료를 `TaskUpdate`로 갱신한다.

## 3단계: 안전 검토

| Task | 담당 | 입력 | 출력 | 의존 |
| --- | --- | --- | --- | --- |
| T04-safety | safety-checker | `01-checklist-roles.md`, `02-schedule.md` | `artifacts/03-safety-check.md` | T02, T03 |

`event-safety-checklist` 스킬 절차를 따른다. 보완 필요 항목이 있으면 safety-checker가 담당 Agent에게 `SendMessage`로 요청하고, 최대 2회 보완 루프를 돈다. 2회 후에도 미해결이면 `TaskGet`으로 확인 후 사용자에게 현재 상태를 보고한다.

## 4단계: 사람 승인 게이트

산출물 저장과 팀 정리(`TeamDelete`)를 마친 뒤, 아래 형식으로 승인을 요청하고 멈춘다.

```md
## 승인 요청

- 승인 대상: {예산 집행 항목 / 외부 업체 계약 / 참석자 명단 발송 / 안전 최종 확인} 중 해당하는 것
- 필요한 이유: 예산·외부계약·개인정보·안전은 되돌리기 어렵거나 사람 확인이 꼭 필요한 영역이다
- 승인하면 일어나는 일: 실행 목록(예약·발주) 확정, 필요 시 D-1 자동 재점검 cron 등록
- 지금 상태: 사람 승인 필요 (아직 결제·발송·계약하지 않음)
- 확인해 주세요: `03-safety-check.md`의 미해결 항목, `01-checklist-roles.md`의 예산 초과 후보
- 보류하면: 보류 사유를 `artifacts/README.md`에 남기고 해당 단계에서 멈춘다
```

승인 없이는 `artifacts/README.md`의 승인 상태를 `사용 가능`으로 바꾸지 않는다.

## 5단계: D-1 자동 재점검 (선택, 승인 후에만)

사용자가 승인하면 "행사 전날 자동 재점검을 등록할까요?"를 묻는다. 동의하면 `schedule` 스킬로 행사일 하루 전(00-event-params.md의 날짜 - 1일)에 safety-checker를 재실행하는 cron을 등록한다. 결과는 `artifacts/04-d1-recheck-log.md`에 남고, 미해결 항목이 있으면 사용자에게 알린다.

## 6단계: 행사 후 회고

행사 종료 후 사용자가 피드백을 주면 retrospective-writer를 호출한다(팀이 이미 정리됐다면 단일 Agent 호출로 충분하다). `05-retrospective.md`와 `improvement-log.md`를 갱신한다.

## 실패 처리

- 입력 부족: 추측하지 않고 질문한다.
- 팀원 1명 실패: `TaskGet` 확인 → `SendMessage`로 원인 확인 → 1회 재시도, 안 되면 재할당.
- 과반 실패: 사용자에게 현재 상태와 선택지를 보고한다.
- 결과 충돌: 지우지 않고 출처를 병기해 사람 판단으로 넘긴다.
- 위험 행동(결제·계약·발송·삭제)은 승인 전 실행하지 않는다.

## 팀 정리

승인 게이트 이전에 `TeamDelete`로 팀을 정리한다. 회고 단계는 별도 호출이므로 팀 재생성이 필요하면 새로 `TeamCreate`한다.
