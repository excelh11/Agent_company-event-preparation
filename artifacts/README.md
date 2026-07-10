# Artifacts Map

## 현재 실행

- 실행 목적: 20명 규모 팀 오프사이트 워크숍(2026-10-16, 예산 530만원으로 조정, 야외 중심+우천 대안 필수) 준비
- 실행 모드: 초기 실행 (2·3·4단계 완료, 4단계 보완 루프 1회 + 데모 승인 결정 반영)
- 마지막 갱신: 2026-07-10
- 최종 산출물: `01-checklist-roles.md` + `02-schedule.md` + `03-safety-check.md` 조합이 이번 실행의 최종 준비 상태
- 승인 상태: **사용 가능 (데모 가정 기반)** — 사용자가 하네스 테스트 목적으로 장소·예산·보험·안전인력 항목을 직접 결정. 실제 행사로 전환 시 아래 "미검증 영역"을 재확인해야 진짜 "사용 가능" 상태가 됨.

## 산출물 지도

| 파일 | 역할 | 만드는 Agent | 다음에 읽는 Agent/단계 | 상태 | 승인 상태 |
| --- | --- | --- | --- | --- | --- |
| `artifacts/00-event-params.md` | 이번 행사 정보(날짜/예산/인원/장소) | Orchestrator (탐색 단계) | prep-coordinator, scheduler | current | 해당 없음 |
| `artifacts/01-checklist-roles.md` | 준비물 목록 + 역할 분담 (+보험 항목 반영) | prep-coordinator | scheduler, safety-checker | current | 사용 가능 (데모 가정 — 실제 대관·보험 재확인 필요) |
| `artifacts/02-schedule.md` | 준비 일정 + 당일 타임라인 | scheduler | safety-checker | current | 사용 가능 (데모 가정) |
| `artifacts/03-safety-check.md` | 안전 점검 결과 (장소·보험·인력 데모 결정 반영, 종합판정 승인 가능) | safety-checker | 사람(승인 게이트) | current | 사용 가능 (데모 가정 — 항목별 실제 확인 필요) |
| `artifacts/04-d1-recheck-log.md` | D-1 자동 재점검 결과 | safety-checker (cron) | 사람 | 미생성 | 해당 없음 — 이번 테스트에서는 미등록 |
| `artifacts/05-retrospective.md` | 행사 후 회고 | retrospective-writer | 다음 실행 Orchestrator | 미생성 | 해당 없음 — 행사 종료 후 |
| `artifacts/improvement-log.md` | 개선 기록 | Orchestrator | 다음 실행 | 미생성 | 해당 없음 |

상태 값: `current`(최신 반영) / `stale`(재검토 필요) / `needs-review`(확인 필요) / `archived`(보관용) / `미생성`(아직 실행 전)

## 부분 재실행 기록

| 날짜 | 바뀐 파일 | stale로 표시한 파일 | 다시 실행할 단계 | 사유 |
| --- | --- | --- | --- | --- |
| 2026-07-10 | `01-checklist-roles.md` (보험 항목 추가) | `03-safety-check.md` (보험 행만) | safety-checker 부분 재검토 | safety-checker가 보험 항목 완전 누락 지적 → prep-coordinator 보완 → safety-checker 반영 완료 |
| 2026-07-10 | `00-event-params.md`(장소), `01-checklist-roles.md`(예산), `03-safety-check.md`(기상대안/화재대피/안전인력/보험/종합판정) | 없음 (Orchestrator가 직접 반영, 재실행 아님) | — | 사용자가 하네스 동작 테스트 목적으로 승인 게이트의 5개 결정 항목(장소/예산/보험/인력자격/D-1 cron)을 직접 지정 |

## 미검증 영역과 승인 필요

- 이번 실행은 **데모 가정 기반 승인**이다. 아래는 실제 행사로 이어갈 경우 반드시 재확인해야 하는 항목이다.
- 미검증: 리조트 실재 여부·대관 가능성, 실제 인근 응급실 연락처, 정식 대피 동선 안내도, 안전 담당자 응급처치 자격증, 보험 증권, 예비비 정식 결재.
- 사람 승인 필요(실제 행사 전환 시): 위 항목들에 대한 정식 확인 및 결재.
- D-1 자동 재점검: 이번 테스트에서는 cron을 등록하지 않았음. 실제 행사로 진행한다면 장소가 실재 확정된 뒤 등록 권장.
- 다음 실행에서 먼저 볼 파일: `03-safety-check.md`(데모 가정 표시 항목), `00-event-params.md`(장소 실재 확인 필요 표시)
