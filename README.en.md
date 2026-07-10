# Company Event Prep Harness

**Language**: [한국어](README.md) | English | [中文](README.zh.md)

A Claude Code harness that runs company/team event preparation as **checklist, role assignment, schedule, safety review, and post-event retrospective**. Designed with the harness-lab pattern (Agent + Skill + Orchestrator + artifacts).

## What it does

It mirrors the steps a human would actually take to prepare an event.

1. **Explore** — confirm date, budget, headcount, venue
2. **Plan** — build the checklist/role assignment (`prep-coordinator`) and the schedule (`scheduler`) in parallel
3. **Safety review** — a separate reviewer (`safety-checker`), who did not author the checklist/schedule, cross-checks them
4. **Human approval gate** — budget spending, external contracts, personal data, and final safety sign-off all require explicit human approval
5. **Post-event retrospective** — after the event, `retrospective-writer` writes the retrospective and improvement log

## Structure

```
.
├── CLAUDE.md                                  # Points to this harness and its natural-language routing rules
├── .claude/
│   ├── agents/                                # Role cards for each teammate
│   │   ├── prep-coordinator.md                # Checklist + role assignment
│   │   ├── scheduler.md                       # Prep schedule + day-of timeline
│   │   ├── safety-checker.md                  # Safety review (reviewer, separate from the authors)
│   │   └── retrospective-writer.md            # Post-event retrospective
│   └── skills/
│       ├── company-event-prep-orchestrator/   # The orchestrator skill that runs the whole flow
│       │   ├── SKILL.md
│       │   └── references/standing-rules.md   # Company standard rules reused across events (persistent instruction)
│       └── event-safety-checklist/            # The checklist procedure safety-checker follows
└── artifacts/                                  # Intermediate and final outputs from each run
    ├── README.md                               # Artifact map (what's where, what's approved)
    ├── 00-event-params.md                      # This event's parameters
    ├── 01-checklist-roles.md                   # Checklist + role assignment table
    ├── 02-schedule.md                          # Prep schedule + day-of timeline
    └── 03-safety-check.md                      # Safety review results
```

## Why it's split into 4 agents

Instead of handing everything to one agent, the work is split into four because each role reads different inputs and applies different judgment.

- **prep-coordinator** — deciding "what to prepare and who owns it" against a budget cap and headcount is its own area of judgment, so it's a separate role.
- **scheduler** — turning a checklist into time allocation and deadline urgency requires different reasoning than building the checklist itself, so it's separate.
- **safety-checker** — the most important separation. If the same agent that wrote the checklist/schedule also graded its own safety, it would be prone to self-assessment bias and likely to miss risks. So this role never authors anything — it only reviews. Author and reviewer are deliberately kept apart.
- **retrospective-writer** — runs at a completely different point in time, after the event, on a different kind of input (real-world feedback, not planning documents), so it's kept separate from the planning-phase agents.

Before adding any new agent, we check: are the inputs/outputs independent, does it need different expertise, is it a responsibility that gets reused across runs, and would quality or safety suffer if it were dropped. All four roles here passed that bar.

## How to use it

You don't need to know the skill name — a natural-language request automatically triggers `company-event-prep-orchestrator`.

- "Help me prepare this quarter's offsite"
- "Just redo the schedule"
- "Redo the safety check only"
- "Prepare this event again, incorporating last time's retrospective"
- "The event is over, write up the retrospective"

## Sample run included

`artifacts/` currently contains a test run for "20-person offsite, budget ₩5,000,000, 2026-10-16." Some items (venue, insurance, safety-staff certification) are **demo assumptions used to verify the harness works end-to-end** — for a real event, the responsible person must re-confirm them (see "Unverified areas" in `artifacts/README.md`).

## Design principles

- The agent that produced the checklist/schedule never self-certifies safety (author and reviewer are separated).
- Budget spending, external contracts, attendee personal data, and final safety sign-off are never marked complete without explicit human approval.
- Rules that repeat across every event (`standing-rules.md`) are kept separate from this event's specific values (`00-event-params.md`), so standing rules carry over cleanly to the next event.

## License

[MIT License](LICENSE)
