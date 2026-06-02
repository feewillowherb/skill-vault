# Spec-Kit 使用指南

## 概述

Spec-Kit 是 GitHub 官方发布的**规范驱动开发（Spec-Driven Development, SDD）**工具包。它通过 `specify-cli`（Python CLI）实现项目脚手架、模板管理和 AI coding agent 集成，帮助开发者以"规范 → 计划 → 任务 → 实现"的流程构建软件。

> 仓库：`repos/spec-kit` · 许可证：MIT · 当前版本：从 main 分支最新提交

## 1. 安装

### 前置条件

- Python 3.11+
- `uv`（推荐）或 `pipx`
- Git
- 支持的 AI coding agent

### 安装命令

```bash
# 使用 uv（推荐）
uv tool install specify-cli --from git+https://github.com/github/spec-kit.git@vX.Y.Z

# 使用 pipx
pipx install git+https://github.com/github/spec-kit.git@vX.Y.Z

# 验证
specify --version
```

## 2. 项目初始化

```bash
# 交互式初始化（自动选择 agent）
specify init my-project

# 指定 agent
specify init my-project --integration claude
specify init my-project --integration copilot
specify init my-project --integration codex

# 在当前目录初始化
specify init --here --integration claude

# 强制合并不空目录
specify init . --force --integration claude

# 同时安装 Preset
specify init my-project --integration claude --preset healthcare-compliance
```

### 初始化后项目结构

```text
my-project/
├── .specify/
│   ├── memory/
│   │   └── constitution.md        # 项目宪法
│   ├── templates/                  # 核心模板（只读）
│   │   ├── constitution-template.md
│   │   ├── spec-template.md
│   │   ├── plan-template.md
│   │   └── tasks-template.md
│   ├── scripts/
│   │   └── bash/                  # 或 ps/
│   ├── init-options.json          # 初始化配置
│   └── integration.json           # agent 集成配置
├── specs/                         # 规范目录
├── CLAUDE.md                      # Agent 指引（由集成生成）
└── .git/
```

### 分支编号策略

```bash
# 顺序编号（默认）：001、002、…、1000
specify init my-project --branch-numbering sequential

# 时间戳编号：20260529-143022
specify init my-project --branch-numbering timestamp
```

## 3. 核心工作流命令

### 3.1 `/speckit.constitution` — 建立项目宪法

```bash
/speckit.constitution Create principles focused on code quality, testing standards, user experience consistency, and performance requirements
```

- **输出**：`.specify/memory/constitution.md`
- **作用**：定义项目的不可变原则，所有后续规划和实现必须遵守
- **模板结构**：Core Principles → Additional Constraints → Development Workflow → Governance

宪法模板是通用的占位符，AI agent 根据用户描述填充具体原则。常见原则包括：

| 条款 | 典型内容 |
|------|----------|
| Library-First | 每个功能先作为独立库开发 |
| CLI Interface | 每个库必须暴露 CLI 接口 |
| Test-First | TDD 强制执行（不可妥协） |
| Simplicity | 最多 3 个项目，不做未来预留 |
| Anti-Abstraction | 直接使用框架，不包装 |

### 3.2 `/speckit.specify` — 创建功能规范

```bash
/speckit.specify Build a task management system with drag-and-drop Kanban boards
```

- **输出**：`specs/<NNN>-<short-name>/spec.md`
- **作用**：将自然语言描述转化为结构化功能规范
- **关键约束**：只描述 WHAT 和 WHY，禁止包含技术栈（HOW）

**执行流程**：

1. 从描述生成 2-4 词短名称（如 `kanban-task-manager`）
2. 确定功能目录编号（扫描 `specs/` 下已有目录）
3. 创建目录并复制 `spec-template.md`
4. 按模板结构填充规范内容
5. 运行质量验证检查清单
6. 如有 `[NEEDS CLARIFICATION]` 标记（最多 3 个），提示用户选择

**Spec 模板结构**（`templates/spec-template.md`）：

| 章节 | 必填 | 内容 |
|------|------|------|
| User Scenarios & Testing | 是 | 按优先级排序的用户故事 + Given/When/Then 验收场景 |
| Edge Cases | 是 | 边界条件和错误场景 |
| Requirements | 是 | 功能需求（FR-001, FR-002…）+ 关键实体 |
| Success Criteria | 是 | 可衡量的成功指标（技术无关） |
| Assumptions | 是 | 假设和默认值 |

### 3.3 `/speckit.clarify` — 澄清需求（可选但推荐）

```bash
/speckit.clarify
```

- **作用**：在 plan 之前做结构化澄清，减少下游返工
- **机制**：基于覆盖率依次提问，记录回答到 Clarifications 章节
- **推荐位置**：在 `/speckit.specify` 之后、`/speckit.plan` 之前

### 3.4 `/speckit.plan` — 创建技术实现计划

```bash
/speckit.plan Use React with TypeScript, Tailwind CSS, and localStorage for persistence
```

- **输出**：`specs/<NNN>-<feature>/plan.md` + 多个辅助文档
- **作用**：将业务需求翻译为技术架构

**生成的辅助文档**：

| 文档 | 内容 |
|------|------|
| `plan.md` | 技术方案概览 + 宪法合规检查 |
| `research.md` | 技术调研和对比（Phase 0） |
| `data-model.md` | 数据结构和实体关系（Phase 1） |
| `contracts/` | API 规范和接口定义（Phase 1） |
| `quickstart.md` | 关键验证场景（Phase 1） |

**Plan 模板核心章节**：

- Technical Context（语言/版本/依赖/存储/测试/目标平台/性能目标）
- Constitution Check（Phase -1 门禁：Simplicity Gate、Anti-Abstraction Gate）
- Project Structure（源码布局）
- Implementation Details（分阶段实现方案）

### 3.5 `/speckit.tasks` — 生成任务清单

```bash
/speckit.tasks
```

- **输出**：`specs/<NNN>-<feature>/tasks.md`
- **输入**：`plan.md`（必须）+ `data-model.md`、`contracts/`、`research.md`（可选）
- **作用**：将实现计划分解为可执行的任务清单

**Tasks 模板特征**：

- 按 User Story 分组，每个故事可独立实现和测试
- `[P]` 标记可并行执行的任务
- `[US1]` / `[US2]` 标记所属用户故事
- Phase 1: Setup → Phase 2: Foundational → Phase 3+: User Stories → Final Phase: Polish
- 每个 Phase 末尾有 Checkpoint

### 3.6 `/speckit.implement` — 执行实现

```bash
/speckit.implement
```

- **作用**：按 tasks.md 的顺序执行所有任务
- **前置条件**：constitution、spec、plan、tasks 均已就绪
- **行为**：验证依赖 → 按 TDD 顺序执行 → 报告进度

### 3.7 可选增强命令

| 命令 | 作用 | 推荐位置 |
|------|------|----------|
| `/speckit.analyze` | 跨工件一致性与覆盖率分析 | `/speckit.tasks` 之后、`/speckit.implement` 之前 |
| `/speckit.checklist` | 生成质量检查清单 | `/speckit.plan` 之后 |
| `/speckit.taskstoissues` | 将任务转换为 GitHub Issues | 任何时候 |

## 4. 完整工作流示例

```bash
# 1. 初始化项目
specify init photo-organizer --integration claude
cd photo-organizer

# 2. 建立宪法
/speckit.constitution Simple Vite app, vanilla HTML/CSS/JS, SQLite local storage, minimal libraries

# 3. 定义功能（WHAT 和 WHY）
/speckit.specify Organize photos into albums grouped by date, drag-and-drop reordering, tile preview

# 4. 澄清需求
/speckit.clarify

# 5. 技术方案（HOW）
/speckit.plan Vite with vanilla HTML/CSS/JS, SQLite via WASM, no framework

# 6. 生成任务
/speckit.tasks

# 7. 质量检查（可选）
/speckit.analyze

# 8. 实现
/speckit.implement
```

## 5. 模板体系

### 模板分辨率优先级

```
优先级 1（最高）: .specify/templates/overrides/    ← 项目本地覆盖
优先级 2:         .specify/presets/templates/       ← Preset 定制
优先级 3:         .specify/extensions/templates/    ← Extension 模板
优先级 4（最低）: .specify/templates/               ← 核心模板
```

模板在**运行时**解析——从最高优先级开始向下查找，使用第一个匹配。

### 核心模板清单

| 模板 | 用途 |
|------|------|
| `constitution-template.md` | 项目宪法 |
| `spec-template.md` | 功能规范 |
| `plan-template.md` | 实现计划 |
| `tasks-template.md` | 任务清单 |
| `checklist-template.md` | 质量检查清单 |

### 命令模板清单（`templates/commands/`）

| 命令模板 | 对应 Slash 命令 |
|----------|-----------------|
| `specify.md` | `/speckit.specify` |
| `constitution.md` | `/speckit.constitution` |
| `clarify.md` | `/speckit.clarify` |
| `plan.md` | `/speckit.plan` |
| `tasks.md` | `/speckit.tasks` |
| `implement.md` | `/speckit.implement` |
| `analyze.md` | `/speckit.analyze` |
| `checklist.md` | `/speckit.checklist` |
| `taskstoissues.md` | `/speckit.taskstoissues` |

## 6. Extension 机制

### Extension 是什么

Extension 为 Spec-Kit **新增能力**——新命令、新模板、新钩子。

### 内置 Extension：Git Branching Workflow

仓库内置了 `git` extension（`extensions/git/`），提供：

- 自动创建 feature branch（`speckit.git.feature`）
- 分支命名规范验证（`speckit.git.validate`）
- 远程仓库检测（`speckit.git.remote`）
- 仓库初始化（`speckit.git.initialize`）
- 自动提交（`speckit.git.commit`）

**Hook 注册**：该 extension 在每个核心命令的 before/after 阶段注册了自动 commit hook（部分为 optional）。

### Extension 配置格式

```yaml
# extension.yml
schema_version: "1.0"
extension:
  id: git
  name: "Git Branching Workflow"
  version: "1.0.0"
provides:
  commands:
    - name: speckit.git.feature
      file: commands/speckit.git.feature.md
hooks:
  before_specify:
    command: speckit.git.feature
    optional: false
  after_specify:
    command: speckit.git.commit
    optional: true
```

### Extension 管理

```bash
specify extension search
specify extension add <extension-name>
specify extension list
specify extension remove <extension-name>
```

## 7. Preset 机制

### Preset 是什么

Preset **定制已有工作流的格式**——修改 spec/plan/tasks 的模板内容，不新增命令。

### 适用场景

- 强制合规格式（如 HIPAA、GDPR）
- 适配特定方法论（Agile、Kanban、DDD）
- 多语言本地化
- 组织标准强制执行

### Preset 管理

```bash
specify preset search
specify preset add <preset-name>
specify preset list
specify preset remove <preset-name>
```

## 8. 支持的 AI Coding Agent

Spec-Kit 支持 30+ AI coding agent，通过 `--integration` 参数选择。安装后以 **Slash Commands**（`.claude/commands/`）或 **Skills**（`.claude/skills/`）形式提供。

### Skills 模式 vs Commands 模式

| 模式 | 产物位置 | 调用方式 | 适用 Agent |
|------|----------|----------|------------|
| Skills | `.claude/skills/` | `/speckit-constitution` | Claude Code、Codex CLI |
| Commands | `.claude/commands/` | `/speckit.constitution` | GitHub Copilot、大多数 Agent |

部分 Agent 默认使用 Skills 模式（Claude Code、Codex CLI、Devin 等），其余默认 Commands 模式。

## 9. 注意事项与限制

1. **强依赖 AI 模型能力**——整个流程高度依赖 LLM 的规范理解和生成质量
2. **宪法条款不可妥协**——Article III（Test-First）等条款是强制性的，不适合所有项目
3. **绿地项目优先**——设计上更适合从零构建，棕地开发需额外适配
4. **Python 生态**——需要 Python 3.11+ 和 `uv`/`pipx`，对 Node.js 团队有额外工具链负担
5. **Phase Gate 的仪式感重**——对于小型项目或快速迭代，宪法检查和 Phase Gate 可能过于繁琐
