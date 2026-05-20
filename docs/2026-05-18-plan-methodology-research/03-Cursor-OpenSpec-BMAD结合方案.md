# Cursor + OpenSpec + BMAD-METHOD 结合方案

## 1. 设计目标

在 epic 级需求上同时获得：

- **BMAD** 的结构化规划、多 Agent 协作、跨项目架构设计；
- **OpenSpec** 的轻量 change 循环、可审计 specs、**唯一权威的 tasks.md**；
- **Cursor** 作为统一入口，无需切换 IDE。

关键约束：**BMAD 与 OpenSpec 之间没有自动化管道，由用户手工衔接。**

## 2. 职责边界

| 维度 | BMAD-METHOD | 用户 | OpenSpec |
|------|-------------|------|----------|
| Epic / PRD | ✅ | 审阅、定稿 | ❌ |
| 拆分为多个可交付单元 | ✅（多个 proposal 草稿） | 选择命名、排序、取舍 | ❌ |
| 跨前端/后端/多仓库架构 | ✅（architecture + 概要 design） | 确认边界与依赖 | 单 change 内细化 design |
| `proposal.md` | ✅ 草稿（规划侧） | 复制到目标项目 / 触发 propose | ✅ 正式 change 内版本 |
| `design.md` | ✅ 概要（关键决策） | 作为 apply 参考传入 | ✅ 可扩展为 change 内 design |
| `specs/` delta | ❌ | — | ✅ |
| **`tasks.md`** | **❌ 禁止** | — | **✅ 唯一来源** |
| 代码实现 | ❌（不用 dev-story） | 验收 | ✅ `/opsx:apply` |
| 归档 | ❌ | — | ✅ `/opsx:archive` |

## 3. 端到端流程

### 阶段 A：BMAD 规划（Cursor + BMAD skills）

```
可选 Analysis → Planning (PRD/UX) → Solutioning (Architecture, Epic 拆分)
```

**输入**：Epic 描述、业务目标、仓库/系统列表（若为多项目）。

**输出**（建议目录，可在 vault `docs/` 或业务项目的 `planning/<epic>/`）：

```
planning/<epic-slug>/
├── project-context.md      # 可选，跨 change 的实现约束
├── architecture.md         # 可选，Winston 架构产出
├── slices/
│   ├── 01-auth-api/
│   │   ├── proposal.md     # OpenSpec propose 输入草稿
│   │   └── design.md       # 概要设计，非实现级 tasks
│   ├── 02-auth-ui/
│   │   ├── proposal.md
│   │   └── design.md
│   └── 03-admin-dashboard/
│       ├── proposal.md
│       └── design.md
└── epic-traceability.md    # 可选：slice ↔ 能力映射
```

**BMAD 对话约束（建议原文粘贴到 Cursor）**：

> 将本 Epic 拆成 N 个可独立归档的 OpenSpec change。每个 slice 只输出 `proposal.md`（Why / What Changes / Capabilities / Impact）和概要 `design.md`（Context / Goals / Decisions）。不要生成 tasks、Story 卡片、冲刺计划或开发步骤列表。

**多项目 / 前后端**：在 `bmad-create-architecture` 中明确：

- 系统边界（BFF、API、Web、移动端）；
- 共享契约（API、事件、共享库）；
- 各 slice 落在哪个 repo（用户后续在各 repo 分别跑 OpenSpec）。

### 阶段 B：用户衔接（手工）

对每个 `slices/<name>/`：

1. 打开**目标代码仓库**（该 slice 的实施位置）。
2. 确保已 `openspec init`。
3. 创建 change，任选其一：
   - **方式 1**：`/opsx:propose <change-id>`，把 BMAD 的 `proposal.md` 内容作为对话上下文粘贴；
   - **方式 2**：手动创建 `openspec/changes/<change-id>/`，写入 `proposal.md`，再运行 OpenSpec 继续生成 specs/design/tasks。
4. 将 BMAD 的 `design.md` 放在 change 目录或对话中声明为 apply 参考；**不要用 BMAD 文件覆盖 OpenSpec 自动生成的 `tasks.md`**。
5. 若存在 `project-context.md`，在 `openspec/config.yaml` 或 apply 提示中引用。

**衔接检查清单**：

- [ ] 一个 BMAD slice ↔ 一个 OpenSpec change（避免一个 change 吞整个 epic）
- [ ] change 命名与 slice 一致（kebab-case）
- [ ] 未从 BMAD 拷贝任何任务列表到仓库
- [ ] 跨仓库 slice 已在对应 repo 各建 change

### 阶段 C：OpenSpec 执行（Cursor + `/opsx:*`）

```
propose → specs → design → tasks → apply → archive
```

| 制品 | 负责方 |
|------|--------|
| `proposal.md` | OpenSpec（可基于 BMAD 草稿扩写） |
| `specs/**/*.md` | OpenSpec |
| `design.md` | OpenSpec（可吸收 BMAD 概要 design） |
| **`tasks.md`** | **仅 OpenSpec** |
| 实现与勾选任务 | `/opsx:apply` |

多个 change 可并行，但建议用户控制 WIP，按 BMAD 拆分顺序或依赖顺序推进。

## 4. 制品格式约定

### 4.1 BMAD 侧 `proposal.md`（草稿）

与 OpenSpec propose 对齐，便于粘贴：

```markdown
## Why
## What Changes
## Capabilities
### New Capabilities
### Modified Capabilities
## Impact
```

### 4.2 BMAD 侧 `design.md`（概要）

```markdown
## Context
## Goals / Non-Goals
## Decisions
## Risks / Trade-offs
```

**不写**：实现步骤、文件级修改列表、checkbox 任务（这些属于 OpenSpec `tasks.md`）。

### 4.3 OpenSpec change（正式）

遵循 `repos/openspec` 的 OPSX 默认 schema；`tasks.md` 只在 OpenSpec 生成后出现。

## 5. 场景示例

### 5.1 全栈 Epic：登录改造

| BMAD 产出 | OpenSpec change |
|-----------|-----------------|
| 架构：统一 Session + API 契约 | — |
| slice `auth-api` | `openspec/changes/auth-api/` |
| slice `auth-web` | `openspec/changes/auth-web/` |

用户先在 monorepo 或各 repo 完成衔接，再分别 `/opsx:apply`。

### 5.2 仅后端 Epic

BMAD 只跑 Planning + Architecture；单个 slice 即可对应一个 change，跳过 UX skill。

## 6. 与旧版「Topic 文件夹自动导入」的差异

| 旧方案 | 本方案 |
|--------|--------|
| Topic 文件夹直接导入 OpenSpec | 用户手工创建 change 并粘贴/拷贝草稿 |
| BMAD 隐含驱动执行 | BMAD **止于** proposal/design 草稿 |
| 未强调 tasks 归属 | **tasks 仅 OpenSpec** |
| 未区分 Cursor 安装 | 明确 `cursor` → `.agents/skills/` |

## 7. 反模式

| 反模式 | 正确做法 |
|--------|----------|
| BMAD `bmad-dev-story` 与 `/opsx:apply` 同时跑 | 只用 OpenSpec apply |
| 把 Story 卡片 Tasks 复制进 `tasks.md` | 删除；让 OpenSpec 重新生成 tasks |
| 一个 OpenSpec change 包含整个 epic | 按 BMAD slice 拆多个 change |
| 期望 BMAD 自动 `openspec init` | 用户在目标 repo 手工 init + propose |
| 在 vault 调研目录跑 `openspec apply` 写代码 | 在真实业务仓库执行 |

## 8. 参考

- [01-Cursor 中使用 BMAD-METHOD](./01-Cursor中使用BMAD-METHOD.md)
- [02-BMAD-METHOD 设计分析](./02-BMAD-METHOD设计分析.md)
- OpenSpec OPSX：`repos/openspec/docs/opsx.md`
- BMAD 安装：`repos/bmad-method/README.md`

---

**文档版本**: 4.0  
**最后更新**: 2026-05-20
