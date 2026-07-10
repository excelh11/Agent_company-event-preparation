# Company Event Prep Harness

**Language**: [한국어](README.md) | [English](README.en.md) | 中文

一个用于筹备公司/团队活动的 Claude Code 工作框架（harness），将流程拆分为**物料清单、分工、日程、安全检查、活动后复盘**。采用 harness-lab 模式（Agent + Skill + Orchestrator + artifacts）设计。

## 它做什么

将人在筹备活动时实际会走的流程原样搬到了这里。

1. **调研** — 确认日期、预算、人数、场地
2. **计划** — 并行生成物料清单/分工表（`prep-coordinator`）和日程表（`scheduler`）
3. **安全检查** — 由未参与制作清单/日程的独立评审者（`safety-checker`）交叉审查
4. **人工审批关卡** — 预算支出、外部合同、个人信息、最终安全确认都必须由人工明确批准
5. **活动后复盘** — 活动结束后由 `retrospective-writer` 编写复盘和改进记录

## 目录结构

```
.
├── CLAUDE.md                                  # 指向本 harness 及其自然语言路由规则
├── .claude/
│   ├── agents/                                # 各角色的职责卡片
│   │   ├── prep-coordinator.md                # 物料清单 + 分工
│   │   ├── scheduler.md                       # 筹备日程 + 当天时间线
│   │   ├── safety-checker.md                  # 安全检查（评审者，与制作者分离）
│   │   └── retrospective-writer.md            # 活动后复盘
│   └── skills/
│       ├── company-event-prep-orchestrator/   # 统筹整个流程的编排（Orchestrator）技能
│       │   ├── SKILL.md
│       │   └── references/standing-rules.md   # 各次活动通用、可复用的公司标准规则（持续性指令）
│       └── event-safety-checklist/            # safety-checker 遵循的检查流程手册
└── artifacts/                                  # 每次执行产生的中间及最终产出
    ├── README.md                               # 产出物地图（内容位置、审批状态）
    ├── 00-event-params.md                      # 本次活动的具体信息
    ├── 01-checklist-roles.md                   # 物料清单 + 分工表
    ├── 02-schedule.md                          # 筹备日程 + 当天时间线
    └── 03-safety-check.md                      # 安全检查结果
```

## 使用方法

无需记住技能名称，用自然语言提出请求即可自动触发 `company-event-prep-orchestrator`。

- "帮我准备这个季度的团建活动"
- "只重新排一下日程"
- "只重新做一次安全检查"
- "结合上次的复盘结果，重新准备这次活动"
- "活动结束了，帮我整理复盘"

## 当前包含的示例执行

`artifacts/` 中目前保存了一次测试执行结果："20 人团建，预算 500 万韩元，2026-10-16"。其中场地、保险、安全人员资质等部分项目属于**为验证 harness 是否能端到端运行而设定的演示假设**，若用于真实活动，负责人必须重新核实（详见 `artifacts/README.md` 中的"未验证事项"）。

## 设计原则

- 制作物料清单/日程的 Agent 不会自行判定安全检查通过（制作者与评审者分离）。
- 预算支出、外部合同、参与者个人信息、最终安全确认，未经人工明确批准，不会被标记为完成。
- 将各次活动通用的规则（`standing-rules.md`）与本次活动专属的数值（`00-event-params.md`）分开管理，使标准规则可以直接沿用到下一次活动。
