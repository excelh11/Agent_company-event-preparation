# Project Harness

이 프로젝트에는 회사/팀 행사 준비 하네스가 있습니다.

## 주요 위치

- 실행 스킬: `.claude/skills/company-event-prep-orchestrator/SKILL.md`
- 안전 점검 매뉴얼: `.claude/skills/event-safety-checklist/SKILL.md`
- 회사 표준 규칙(지속 규칙, 거의 안 바뀜): `.claude/skills/company-event-prep-orchestrator/references/standing-rules.md`
- Agent: `.claude/agents/` (prep-coordinator, scheduler, safety-checker, retrospective-writer)
- 중간·최종 산출물: `artifacts/`

## 자연어 라우팅

사용자가 스킬명을 직접 입력하지 않아도 아래처럼 판단되면 `company-event-prep-orchestrator`를 먼저 사용합니다.

- "이번 워크숍/오프사이트 준비 좀 도와줘"
- "행사 준비물이랑 역할 분담 정리해줘"
- "행사 일정만 다시 짜줘"
- "안전 점검만 다시 해줘"
- "행사 전날 재점검 걸어줘"
- "지난번 회고 반영해서 이번 행사 다시 준비해줘"
- "행사 끝났는데 회고 정리해줘"

## 하네스 변경 이력

| 날짜 | 변경 내용 | 대상 | 사유 |
| --- | --- | --- | --- |
| 2026-07-10 | 초기 구성 (Agent 4명, Orchestrator, 안전 점검 스킬, 표준 규칙 분리) | 전체 | 회사 행사 준비 반복 업무 하네스화 |
