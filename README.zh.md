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
## 为什么拆成 4 个 Agent

没有把所有工作都交给一个 Agent，而是拆成 4 个，是因为每个角色所依据的输入和判断标准都不一样。

- **prep-coordinator** — 依据预算上限和人数来决定"准备什么、由谁负责"，这是一个独立的专业判断领域，因此单独设置。
- **scheduler** — 把清单转化为时间分配和截止日期紧迫度，需要和制作清单不同的判断方式，因此分离出来。
- **safety-checker** — 最关键的分离原因。如果由制作清单/日程的同一个 Agent 自行判定安全是否合格,很容易因为"自我评估偏差"而漏掉风险。因此这个角色不制作任何内容，只负责审查——刻意将制作者和评审者分开。
- **retrospective-writer** — 在活动结束后、完全不同的时间点运行，依据的是现场反馈这类不同性质的输入，而非计划文档，因此与计划阶段的 Agent 分开。

在新增任何 Agent 之前，我们都会确认："输入/输出是否独立、是否需要不同的专业能力、是否是会被反复复用的职责、如果去掉会不会影响质量或安全"。这四个角色都通过了这个标准，因此各自成为独立的 Agent。


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

## 许可证

[MIT License](LICENSE)
