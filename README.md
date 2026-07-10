# Company Event Prep Harness

회사/팀 행사 준비를 **준비물, 역할 분담, 일정, 안전 점검, 행사 후 회고**로 나눠 진행하는 Claude Code 하네스입니다. [harness-lab](https://github.com/anthropics) 방식(Agent + Skill + Orchestrator + artifacts)으로 설계했습니다.

## 무엇을 하는가

행사 하나를 준비할 때 사람이 밟는 절차를 그대로 옮겼습니다.

1. **탐색** — 날짜, 예산, 인원, 장소 확인
2. **계획** — 준비물/역할 분담(`prep-coordinator`)과 일정표(`scheduler`)를 병렬 작성
3. **안전 점검** — 준비물/일정을 만들지 않은 별도 평가자(`safety-checker`)가 교차 검토
4. **사람 승인 게이트** — 예산 집행, 외부 계약, 개인정보, 안전 최종 확인은 사람이 직접 승인
5. **행사 후 회고** — 종료 후 `retrospective-writer`가 회고와 개선 기록 작성

## 구조

```
.
├── CLAUDE.md                                  # 이 하네스의 존재와 자연어 라우팅 규칙
├── .claude/
│   ├── agents/                                # 팀원 역할 카드
│   │   ├── prep-coordinator.md                # 준비물 + 역할 분담
│   │   ├── scheduler.md                       # 준비 일정 + 당일 타임라인
│   │   ├── safety-checker.md                  # 안전 점검(평가자, 생성자와 분리)
│   │   └── retrospective-writer.md            # 행사 후 회고
│   └── skills/
│       ├── company-event-prep-orchestrator/   # 전체 진행을 관리하는 팀장 스킬
│       │   ├── SKILL.md
│       │   └── references/standing-rules.md   # 행사마다 재사용하는 회사 표준 규칙(지속 규칙)
│       └── event-safety-checklist/            # safety-checker가 따르는 점검 매뉴얼
└── artifacts/                                  # 실행할 때마다 쌓이는 중간·최종 산출물
    ├── README.md                               # 산출물 지도(무엇이 어디 있고, 승인 상태가 뭔지)
    ├── 00-event-params.md                      # 이번 행사 정보
    ├── 01-checklist-roles.md                   # 준비물 + 역할 분담표
    ├── 02-schedule.md                          # 준비 일정 + 당일 타임라인
    └── 03-safety-check.md                      # 안전 점검 결과
```

## 사용 방법

스킬명을 몰라도 자연어로 요청하면 자동으로 `company-event-prep-orchestrator`가 실행됩니다.

- "이번 분기 오프사이트 준비 좀 도와줘"
- "일정만 다시 짜줘"
- "안전 점검만 다시 해줘"
- "지난번 회고 반영해서 이번 행사 다시 준비해줘"
- "행사 끝났는데 회고 정리해줘"

## 지금 들어 있는 예시 실행

`artifacts/`에는 "20명 규모 오프사이트, 예산 500만원, 2026-10-16" 조건으로 실행한 테스트 결과가 들어 있습니다. 장소·보험·안전 인력 자격 등 일부 항목은 **하네스 동작을 확인하기 위한 데모 가정**이며, 실제 행사라면 담당자가 직접 재확인해야 합니다(`artifacts/README.md`의 "미검증 영역" 참고).

## 하네스 설계 원칙

- 준비물/일정을 만든 Agent가 스스로 안전을 통과 처리하지 않습니다(생성자-평가자 분리).
- 예산 집행, 외부 계약, 참석자 개인정보, 안전 최종 확인은 사람 승인 없이 완료 처리하지 않습니다.
- 회사마다 반복되는 표준 규칙(`standing-rules.md`)과 이번 행사만의 값(`00-event-params.md`)을 분리해, 다음 행사 때 표준 규칙은 그대로 재사용합니다.
