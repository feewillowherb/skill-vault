# Spec-Kit 与 OpenSpec 对比分析

## 1. 哲学差异

### Spec-Kit：宪法驱动的严格规范

核心主张是**"规范即源文件"**——规范不是代码的附属品，而是生成代码的主源。开发团队专注于意图表达和创造力，代码是从规范的实现计划中"转化"出来的产物。

方法论强调：

- **意图驱动开发**：先定义 WHAT 和 WHY，再考虑 HOW
- **多步精炼**：规范 → 计划 → 任务 → 实现，每步都有质量门禁
- **宪法约束**：不可变原则确保跨时间、跨模型的一致性
- **模板约束 LLM 行为**：通过结构化模板防止 AI 过度发散或遗漏

### OpenSpec：工件驱动的灵活迭代

核心主张是**"fluid not rigid"**——无阶段门禁，工件随时可更新，依赖关系只是"使能器"而非"门禁"。

方法论强调：

- **Delta 规范**：只描述变更（ADDED/MODIFIED/REMOVED），不重写整体
- **棕地优先**：天然适配已有代码库的增量修改
- **工件即状态**：文件系统存在性决定工件状态（BLOCKED → READY → DONE）
- **渐近式严格度**：大部分变更用 Lite spec，高风险变更才用 Full spec

## 2. 工作流对比

### Spec-Kit 工作流

```text
                    宪法 (constitution)
                          │
                          ▼
    ┌──────────────────────────────────────┐
    │  /speckit.constitution               │  建立不可变原则
    └──────────────────┬───────────────────┘
                       │
                       ▼
    ┌──────────────────────────────────────┐
    │  /speckit.specify                    │  WHAT + WHY（技术无关）
    │  ├─ 自动编号 + 分支创建              │
    │  ├─ 模板填充 + 质量验证              │
    │  └─ [NEEDS CLARIFICATION] 最多 3 个   │
    └──────────────────┬───────────────────┘
                       │ (推荐)
                       ▼
    ┌──────────────────────────────────────┐
    │  /speckit.clarify                    │  结构化澄清
    └──────────────────┬───────────────────┘
                       │
                       ▼
    ┌──────────────────────────────────────┐
    │  /speckit.plan                      │  HOW（技术方案）
    │  ├─ 宪法合规检查 (Phase -1 Gates)    │
    │  ├─ research.md                      │
    │  ├─ data-model.md                    │
    │  ├─ contracts/                      │
    │  └─ quickstart.md                    │
    └──────────────────┬───────────────────┘
                       │
                       ▼
    ┌──────────────────────────────────────┐
    │  /speckit.tasks                      │  任务分解
    │  ├─ 按 User Story 分组               │
    │  ├─ [P] 并行标记                     │
    │  └─ Checkpoint 验证点                │
    └──────────────────┬───────────────────┘
                       │
                       ▼
    ┌──────────────────────────────────────┐
    │  /speckit.implement                 │  执行实现
    └──────────────────────────────────────┘
```

**特征**：线性分阶段，每阶段有明确的输入/输出和质量门禁。回退到前阶段需要显式操作。

### OpenSpec OPSX 工作流

```text
    ┌──────────────────────────────────────────────────┐
    │               ACTIONS（非 PHASES）                │
    │                                                  │
    │   explore ◄──► propose ◄──► apply ◄──► archive  │
    │      │            │           │           │       │
    │      └────────────┴───────────┴───────────┘       │
    │                 任意顺序                           │
    └──────────────────────────────────────────────────┘

    工件依赖 DAG：
                    proposal
                       │
             ┌─────────┴─────────┐
             │                   │
             ▼                   ▼
          specs              design
             │                   │
             └─────────┬─────────┘
                       │
                       ▼
                    tasks
```

**特征**：工件之间有依赖关系（DAG），但依赖只是"使能"而非"门禁"——你可以在任何时候编辑任何工件。实现过程中发现 design 有问题，直接修改 design.md 然后继续。

## 3. 技术架构对比


| 维度        | Spec-Kit              | OpenSpec                           |
| --------- | --------------------- | ---------------------------------- |
| CLI 语言    | Python（`specify-cli`） | TypeScript（`@fission-ai/openspec`） |
| 包管理       | `uv` / `pipx`         | `npm` / `pnpm` / `yarn` / `bun`    |
| 运行时       | Python 3.11+          | Node.js 20.19.0+                   |
| Agent 集成数 | 30+                   | 25+                                |
| 模板引擎      | Markdown 模板 + Jinja2  | YAML Schema + Markdown 模板          |
| 定制机制      | Extension + Preset    | Custom Schema + Project Config     |
| 工件状态检测    | CLI 内部逻辑              | 文件系统存在性                            |
| 依赖管理      | 无（线性流程）               | DAG 拓扑排序                           |


## 4. 规范格式对比

### Spec-Kit 规范结构（全量）

```text
specs/001-kanban-board/
├── spec.md              # 完整功能规范
│   ├── User Scenarios   # 按优先级排序的用户故事
│   ├── Edge Cases       # 边界条件
│   ├── Requirements      # 功能需求
│   ├── Success Criteria # 成功指标
│   └── Assumptions      # 假设
├── plan.md              # 技术实现计划
├── tasks.md             # 任务清单
├── data-model.md        # 数据模型
├── contracts/           # API 合约
├── quickstart.md       # 验证场景
└── research.md          # 技术调研
```

每个 feature 是一个**自包含的全量规范包**。不区分"当前系统状态"和"变更内容"。

### OpenSpec 规范结构（Delta）

```text
openspec/
├── specs/               # 系统行为源（Source of Truth）
│   ├── auth/spec.md      # 当前认证行为
│   └── ui/spec.md       # 当前 UI 行为
└── changes/
    ├── add-dark-mode/    # 变更包
    │   ├── proposal.md   # 为什么做、做什么
    │   ├── design.md     # 怎么做
    │   ├── tasks.md      # 实施步骤
    │   └── specs/        # Delta 规范
    │       └── ui/
    │           └── spec.md
    └── archive/          # 已完成变更
        └── 2025-01-23-add-dark-mode/
```

`specs/` 是**系统行为的源文件**。每个 change 通过 **Delta 规范**（ADDED/MODIFIED/REMOVED）描述变更。归档时 delta 合并到主规范。

### Delta 规范示例

```markdown
# Delta for UI Theme

## ADDED Requirements

### Requirement: Dark Mode Toggle
The system MUST provide a dark mode toggle in the settings panel.

#### Scenario: User enables dark mode
- GIVEN the user is on the settings page
- WHEN the user toggles the dark mode switch
- THEN the UI immediately switches to dark theme
- AND the preference is persisted to localStorage

## MODIFIED Requirements

### Requirement: Color Scheme
The system MUST use CSS custom properties for theming.
(Previously: hardcoded color values)

## REMOVED Requirements

### Requirement: Light-Only Mode
(Deprecated in favor of theme system)
```

**Delta 模型的优势**：

- **清晰**：只看变更，不需要脑内 diff
- **冲突避免**：两个 change 可以修改同一规范文件的不同 requirement
- **棕地适配**：大多数工作是修改现有行为，delta 使其成为一等公民
- **审计友好**：归档时变更包完整保留，包含 why + what + how

## 5. 质量保障机制对比

### Spec-Kit：宪法 + Phase Gates

**宪法**（`constitution.md`）包含不可变原则：


| 条款               | 内容         | 强制性      |
| ---------------- | ---------- | -------- |
| Library-First    | 每个功能先作为独立库 | 是        |
| CLI Interface    | 库必须暴露 CLI  | 是        |
| Test-First       | TDD 强制执行   | **不可妥协** |
| Simplicity       | ≤3 个项目     | 是        |
| Anti-Abstraction | 直接使用框架     | 是        |


**Phase Gates**（在 plan 阶段检查）：

- Simplicity Gate（Article VII）：是否 ≤3 个项目？是否无未来预留？
- Anti-Abstraction Gate（Article VIII）：是否直接使用框架？
- Integration-First Gate（Article IX）：合约是否已定义？

未通过的门禁必须记录理由到"Complexity Tracking"章节。

### OpenSpec：渐进式严格度

- **Lite spec**（默认）：简短行为需求 + 清晰范围 + 少量验收检查
- **Full spec**（高风险变更）：完整 RFC 2119 需求 + Given/When/Then 场景

大部分变更使用 Lite spec，只有跨团队、API 合约、安全隐私等高风险场景才需要 Full spec。

### 对比总结


| 质量维度  | Spec-Kit                | OpenSpec             |
| ----- | ----------------------- | -------------------- |
| 原则执行  | 宪法强制（不可变）               | Config 中的 rules（软约束） |
| 阶段检查  | Phase Gates（硬门禁）        | 依赖 DAG（软使能）          |
| 规范严格度 | 统一严格                    | 渐进式（Lite/Full）       |
| 测试策略  | TDD 强制                  | 可选                   |
| 澄清机制  | `/speckit.clarify`（结构化） | `/opsx:explore`（自由式） |


## 6. 扩展与定制机制对比

### Spec-Kit：Extension + Preset

**Extension**（新增能力）：

```yaml
# extension.yml
extension:
  id: my-extension
  name: "My Extension"
provides:
  commands:
    - name: speckit.my-command
      file: commands/my-command.md
hooks:
  before_specify:
    command: speckit.my-command
    optional: true
```

- 注册 before/after hooks
- 提供新命令
- 提供 config 模板

**Preset**（定制模板）：

- 覆盖核心模板和 extension 模板
- 不新增命令
- 可堆叠，有优先级解析

### OpenSpec：Custom Schema + Project Config

**Custom Schema**：

```yaml
# openspec/schemas/research-first/schema.yaml
name: research-first
artifacts:
  - id: research
    generates: research.md
    requires: []
  - id: proposal
    generates: proposal.md
    requires: [research]
  - id: tasks
    generates: tasks.md
    requires: [proposal]
```

- 定义自己的工件类型和依赖关系
- `schema init`、`schema fork` 快速创建

**Project Config**：

```yaml
# openspec/config.yaml
schema: spec-driven
context: |
  Tech stack: TypeScript, React, Node.js
rules:
  specs:
    - Use Given/When/Then format
```

- `context` 注入到所有工件的指令中
- `rules` 按工件类型注入特定规则

## 7. 文件系统结构对比

### Spec-Kit

```text
project/
├── .specify/
│   ├── memory/constitution.md
│   ├── templates/              # 核心 + preset + extension + override
│   ├── scripts/bash/
│   ├── init-options.json
│   └── integration.json
├── specs/
│   └── 001-feature/
│       ├── spec.md
│       ├── plan.md
│       ├── tasks.md
│       ├── data-model.md
│       ├── contracts/
│       ├── quickstart.md
│       └── research.md
└── .git/
```

### OpenSpec

```text
project/
├── openspec/
│   ├── config.yaml
│   ├── specs/                 # Source of Truth
│   │   ├── domain-1/spec.md
│   │   └── domain-2/spec.md
│   ├── schemas/
│   │   └── spec-driven/
│   │       ├── schema.yaml
│   │       └── templates/
│   └── changes/
│       ├── active-change/
│       │   ├── proposal.md
│       │   ├── design.md
│       │   ├── tasks.md
│       │   └── specs/         # Delta specs
│       └── archive/
│           └── 2025-01-23-completed-change/
└── .git/
```

### 结构设计差异


| 设计选择      | Spec-Kit                     | OpenSpec                          |
| --------- | ---------------------------- | --------------------------------- |
| 规范位置      | `specs/<feature>/spec.md`    | `openspec/specs/<domain>/spec.md` |
| 变更管理      | 每个 feature 一个目录              | `openspec/changes/<change>/`      |
| 规范生命周期    | 跟随 feature 分支                | 增量合并（delta → 主规范）                 |
| 历史保留      | Git 历史                       | `changes/archive/` 保留完整变更包        |
| 多 feature | `specs/001/`、`specs/002/` 并存 | `changes/` 中多个目录并行                |


## 8. 适用场景分析

### Spec-Kit 更适合


| 场景             | 原因                        |
| -------------- | ------------------------- |
| 企业级合规项目        | 宪法强制原则 + Phase Gates 确保合规 |
| 绿地项目           | 全量规范模型天然适配从零构建            |
| 大团队协作          | 不可变原则确保跨团队成员的一致性          |
| 关键系统（医疗/金融/安全） | TDD 强制 + 架构门禁 + 质量检查清单    |
| 长期维护项目         | 宪法确保架构一致性不被侵蚀             |


### OpenSpec 更适合


| 场景       | 原因                              |
| -------- | ------------------------------- |
| 棕地开发     | Delta 规范天然适配增量修改                |
| 快速迭代     | 无阶段锁定，工件随时可更新                   |
| 多仓库项目    | Workspace 协调 + context-store 支持 |
| 小团队/个人项目 | 轻量级，最低仪式感                       |
| 需求频繁变化   | 流式迭代，不需要"回到上一阶段"的显式操作           |


## 9. 对现有 BMAD + OpenSpec 工作流的评估

### 当前工作流

```
BMAD（规划阶段）: npx bmad-method install → Epic 规划 → 架构 → proposal.md
    ↓（手工导入）
OpenSpec（实施阶段）: /opsx:propose → /opsx:apply → /opsx:archive
```

### Spec-Kit 能补充什么


| Spec-Kit 能力        | 当前是否已有替代                                        | 是否值得引入                             |
| ------------------ | ----------------------------------------------- | ---------------------------------- |
| Constitution（项目原则） | 可用 OpenSpec `config.yaml` 的 `context` + `rules` | **不引入**，用 config.yaml 替代           |
| Clarify（结构化澄清）     | 可用 `/opsx:explore`                              | **不引入**，explore 足够灵活               |
| Plan 模板（技术方案）      | BMAD 的 proposal.md + design.md 已覆盖              | **不引入**，BMAD 更擅长规划                 |
| Tasks 模板（按 US 分组）  | OpenSpec 的 tasks.md 已覆盖                         | **不引入**，OpenSpec 的 delta 模型更好      |
| Quality Gates      | 无直接替代                                           | **可借鉴**，在 config.yaml rules 中添加检查项 |
| Extension 机制       | OpenSpec 的 Schema + Config                      | **不引入**，概念不同                       |


### 结论

**不建议在现有工作流中引入 Spec-Kit**。原因：

1. **哲学冲突**：Spec-Kit 的宪法驱动 + 线性门禁与 OpenSpec 的流式迭代 + Delta 规范根本冲突
2. **功能重叠**：BMAD 已覆盖规划阶段，OpenSpec 已覆盖实施阶段，Spec-Kit 在两者之间找不到位置
3. **认知负担**：引入第三套方法论会增加团队的学习成本和流程复杂度
4. **工具链差异**：Python vs Node.js，两套 CLI 并存增加维护成本

**可借鉴的做法**：

- 在 OpenSpec `config.yaml` 的 `rules` 中添加类似 Spec-Kit Constitution 的质量检查项
- 在 BMAD 规划中参考 Spec-Kit 的 Clarify 步骤，做更结构化的需求澄清

