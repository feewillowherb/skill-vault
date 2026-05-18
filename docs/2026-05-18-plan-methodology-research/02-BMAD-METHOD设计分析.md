# BMAD-METHOD 设计分析

## 1. 概述

BMAD-METHOD（Build More Architect Dreams）是一个 AI 驱动的结构化敏捷开发框架，提供了从产品构想到代码交付的**全生命周期**方法论。

**来源**: `repos/bmad-method`
**当前版本**: v6
**定位**: 全生命周期 AI 敏捷开发框架

### 核心主张

> "传统 AI 工具替你思考，产生平庸的结果。BMAD Agent 和引导式工作流作为专家协作者，通过结构化流程引导你和 AI 共同产出最佳思考。"

## 2. 四阶段生命周期

### 2.1 总体流程

```
Phase 1          Phase 2          Phase 3           Phase 4
Analysis    →    Planning    →    Solutioning  →    Implementation
(可选)           (必须)           (必须)             (必须)

┌──────────┐    ┌──────────┐    ┌──────────┐      ┌──────────┐
│ 头脑风暴  │    │   PRD    │    │ 架构设计  │      │ 冲刺管理  │
│ 市场调研  │    │  需求文档 │    │ Epic拆分  │      │ 故事实现  │
│ 技术调研  │    │ UX设计   │    │ 就绪检查  │      │ 代码审查  │
│ 产品简报  │    │          │    │          │      │ 回顾复盘  │
│ PRFAQ    │    │          │    │          │      │          │
└──────────┘    └──────────┘    └──────────┘      └──────────┘
    ↓               ↓               ↓                ↓
  Agent:          Agent:          Agent:            Agent:
  Mary/John       John/Sally      Winston/Amelia    Amelia
```

### 2.2 Phase 1: Analysis（分析阶段，可选）

**目的**: 在投入开发之前，验证想法和探索问题空间。

| 工作流 | 描述 | 核心输出 |
|--------|------|----------|
| `bmad-brainstorming` | 引导式头脑风暴 | 创意清单、优先级排序 |
| `bmad-market-research` | 市场调研和验证 | 竞品分析、市场定位 |
| `bmad-domain-research` | 领域知识研究 | 领域模型、术语表 |
| `bmad-technical-research` | 技术可行性调研 | 技术选型报告 |
| `bmad-product-brief` | 产品简报 | 产品愿景文档 |
| `bmad-prfaq` | Working Backwards 压力测试 | PRFAQ 文档 |

**设计亮点**: 使用 Amazon 的 "Working Backwards" 方法论，从客户视角反向验证产品概念。

### 2.3 Phase 2: Planning（规划阶段）

**目的**: 明确"做什么"和"为谁做"。

| 工作流 | 描述 | 核心输出 |
|--------|------|----------|
| `bmad-prd` | 产品需求文档 | PRD (Create/Update/Validate) |
| `bmad-create-ux-design` | UX 设计规范 | UX 规格文档 |

**PRD 工作流设计**（v6 重大重设计）：

```
bmad-prd 工作流
├── Intent: Create（创建）
│   ├── Discovery Shape: Brain dump → Stakes calibration → Working mode
│   ├── "Essential Spine" 模板
│   └── 自适应填充
├── Intent: Update（更新）
│   ├── 基于现有 PRD 的增量修改
│   └── 变更影响追踪
└── Intent: Validate（验证）
    ├── 内置验证管线
    ├── 质量评分矩阵
    └── HTML + Markdown 双格式报告
```

### 2.4 Phase 3: Solutioning（方案设计阶段）

**目的**: 决定"怎么做"并将工作拆解为可执行单元。

| 工作流 | 描述 | 核心输出 |
|--------|------|----------|
| `bmad-create-architecture` | 技术架构设计 | 架构文档 + ADR |
| `bmad-create-epics-and-stories` | Epic 和 Story 拆分 | Epic 文档、Story 卡片 |
| `bmad-check-implementation-readiness` | 实施就绪检查 | 就绪报告 |
| `bmad-generate-project-context` | 项目上下文生成 | project-context.md |

**Architecture 工作流**：

```
bmad-create-architecture
├── Step 1: 加载 PRD + UX 设计
├── Step 2: 引导式架构决策
├── Step 3: ADR (Architecture Decision Record) 记录
├── Step 4: 微文件架构设计
├── Step 5: 状态追踪（frontmatter）
└── Step 6: 架构决策日志
```

**关键设计**: `project-context.md` 文件为后续 AI Agent 提供关键实现规则，确保不同 Agent 在不同阶段的代码生成保持一致。

### 2.5 Phase 4: Implementation（实施阶段）

**目的**: 逐个 Story 构建项目。

| 工作流 | 描述 | 核心输出 |
|--------|------|----------|
| `bmad-sprint-planning` | 冲刺规划 | sprint-status.yaml |
| `bmad-create-story` | Story 准备 | 完整 Story 卡片 |
| `bmad-dev-story` | Story 开发 | 代码 + 测试 |
| `bmad-code-review` | 代码审查 | 审查报告 |
| `bmad-correct-course` | 航向修正 | 变更影响分析 |
| `bmad-sprint-status` | 冲刺状态 | 进度报告 |
| `bmad-retrospective` | 回顾复盘 | 改进建议 |

**Story 开发工作流**（核心）：

```
bmad-dev-story
├── 1. 加载 Story 卡片 + 项目上下文
├── 2. TDD 循环
│   ├── Red:  编写失败测试
│   ├── Green: 最小实现通过测试
│   └── Refactor: 清理和优化
├── 3. 顺序执行 Tasks
├── 4. 更新 Story 完成状态
└── 5. 生成变更日志
```

### 2.6 Quick Flow（快速流程）

针对小型的、明确的工作，提供简化的统一流程：

```
bmad-quick-dev
└── 单一工作流覆盖分析 → 规划 → 实现
```

## 3. 六角色 Agent 系统

BMAD-METHOD 的核心创新之一是专业化 Agent 角色系统。每个 Agent 有独立的人格、专业领域和沟通风格。

### 3.1 Agent 矩阵

| 角色 | 代号 | 专业领域 | 沟通风格 | 核心原则 |
|------|------|----------|----------|----------|
| Mary | 📊 BA | 商业分析、战略研究 | "寻宝者的兴奋 + 麦肯锡备忘录的结构" | 证据驱动、精确需求、全利益相关者 |
| Paige | 📚 TW | 技术文档、知识管理 | "耐心教育者——像教朋友一样解释" | 为读者而写、图表胜千言、因人而异 |
| John | 📋 PM | 产品管理、需求定义 | "侦探般追问'为什么'，直接、数据导向" | 用户访谈驱动、最小验证、用户价值优先 |
| Sally | 🎨 UX | 用户体验、交互设计 | "用文字画画，让用户故事让人感同身受" | 用户需求驱动、从简入繁、数据引导创意 |
| Winston | 🏗️ SA | 系统架构、技术决策 | "冷静务实，平衡可能性和应该性" | 三次法则、保守技术、开发效率即架构 |
| Amelia | 💻 SSE | 软件工程、代码实现 | "极简，用文件路径和 AC ID 说话" | 测试优先、红绿重构、按序执行 |

### 3.2 Agent 人格设计

每个 Agent 的定义包含：

```
Agent Definition
├── Identity: 身份和灵感来源
│   └── 例: Winston channels Martin Fowler + Werner Vogels
├── Communication Style: 沟通风格
│   └── 例: "Calm and pragmatic"
├── Principles: 行为原则（3-5条）
│   └── 例: "Rule of Three before abstraction"
├── Scope: 负责的阶段和任务
│   └── 例: Phase 3 Solutioning + Phase 4
└── Anti-patterns: 避免的行为
    └── 例: "Never gold-plate architecture"
```

### 3.3 Party Mode（多 Agent 协作）

BMAD 支持在单一会话中引入多个 Agent 角色：

- **独立思考**: 每个 Agent 独立推理，不是由单个 LLM 角色扮演
- **碰撞讨论**: 不同视角的 Agent 讨论同一问题
- **综合决策**: 从多角度形成更全面的决策

## 4. 渐进式上下文传递

### 4.1 核心机制

BMAD 最精妙的设计是其**上下文渐进传递**机制——每个阶段的输出自动成为下一个阶段的输入。

```
上下文流
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│  Product Brief ──→ PRD ──→ UX Design ──→ Architecture      │
│       │              │          │              │             │
│       ↓              ↓          ↓              ↓             │
│  [Decision Log] [PRD Addendum] [UX Spec]  [ADR + Context]   │
│                                           │                  │
│                                           ↓                  │
│                                    Epics & Stories            │
│                                           │                  │
│                                           ↓                  │
│                                    Sprint Status (YAML)      │
│                                           │                  │
│                                           ↓                  │
│                                    Story Cards                │
│                                           │                  │
│                                           ↓                  │
│                                    Implementation            │
│                                                             │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  project-context.md (贯穿所有阶段的持久上下文)         │   │
│  │  .decision-log.md (跨阶段决策追踪)                    │   │
│  │  persistent_facts (Agent 加载的持久化事实)             │   │
│  └──────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

### 4.2 三层上下文

| 层次 | 载体 | 生命周期 | 用途 |
|------|------|----------|------|
| 阶段制品 | PRD、架构文档、Story 卡片 | 阶段内 | 阶段间传递上下文 |
| 项目上下文 | project-context.md | 项目级 | 跨阶段一致性规则 |
| 持久事实 | persistent_facts 配置 | 全局 | 所有 Agent 共享的基础事实 |

### 4.3 设计意义

这种渐进式传递解决了 LLM 上下文窗口有限的核心问题：

1. **分段处理**: 每个阶段只需要处理当前阶段的上下文 + 前序阶段制品
2. **显式传递**: 通过文件而非对话历史传递上下文，避免信息丢失
3. **可审计**: 所有中间制品都是可读的文件，方便审查和回溯

## 5. 制品模板体系

### 5.1 核心制品

| 制品 | 模板 | 格式 | 创建阶段 |
|------|------|------|----------|
| Product Brief | 标准模板 | Markdown | Analysis |
| PRD | Essential Spine | Markdown + Frontmatter | Planning |
| UX Design | 指导模板 | Markdown | Planning |
| Architecture | 微文件模板 | Markdown + ADR | Solutioning |
| Epic | 标准模板 | Markdown | Solutioning |
| Story Card | 综合模板 | Markdown + Frontmatter | Implementation |
| Sprint Status | YAML Schema | YAML | Implementation |
| Project Context | 规则模板 | Markdown | Solutioning |

### 5.2 Story Card 模板结构

Story 卡片是 BMAD 实施阶段的核心制品，包含完整的实现上下文：

```yaml
---
# Frontmatter: 元数据和状态追踪
story_id: STORY-001
epic_id: EPIC-001
status: in-progress
tasks_total: 5
tasks_complete: 0
---

# Story 标题和描述
# 验收标准 (Acceptance Criteria)
# 实现任务列表 (Tasks)
# 开发笔记 (Dev Notes)
# 参考文档 (References)
# 变更日志 (Change Log)
```

### 5.3 Sprint Status YAML

```yaml
sprint:
  name: "Sprint 1"
  start_date: "2026-05-18"
  stories:
    - id: STORY-001
      title: "User Registration"
      status: in-progress
      tasks_total: 5
      tasks_complete: 2
    - id: STORY-002
      title: "Login Flow"
      status: pending
```

## 6. 可扩展性架构

### 6.1 模块系统

BMAD 采用模块化架构：

```
BMAD-METHOD Architecture
├── core-skills/          # 核心技能（跨模块共享）
│   ├── bmad-create-doc
│   ├── bmad-editorial-review
│   ├── bmad-adversarial-review
│   └── ...
├── bmm-skills/           # BMAD 方法模块（生命周期工作流）
│   ├── 1-analysis/
│   ├── 2-plan-workflows/
│   ├── 3-solutioning/
│   └── 4-implementation/
└── customize.toml        # 用户自定义层
```

### 6.2 定制化层

通过 `customize.toml` 支持三个层面的定制：

| 层面 | 描述 | 示例 |
|------|------|------|
| Agent 人格覆盖 | 修改 Agent 的沟通风格和原则 | 让 Winston 更激进取新 |
| 工作流步骤修改 | 调整工作流的步骤和流程 | 跳过 UX 设计步骤 |
| 文档标准 | 强制文档格式和内容标准 | 统一使用中文文档 |
| 外部源集成 | 引入外部知识和工具 | 集成特定 API 文档 |

### 6.3 意图驱动工作流

每个工作流支持三种意图（Intent）：

```
bmad-prd
├── Create  → 从零创建 PRD
├── Update  → 基于现有 PRD 增量修改
└── Validate → 验证 PRD 质量
```

这种设计允许工作流在不同场景下复用，而不需要为每种场景创建独立的工作流。

## 7. 设计亮点

### 7.1 证据分级系统

BMAD 在调研和审查中使用证据分级：

| 级别 | 描述 | 可信度 |
|------|------|--------|
| Confirmed | 已验证的事实 | 最高 |
| Deduced | 从已知事实推导 | 中等 |
| Hypothesized | 合理假设 | 需验证 |

### 7.2 微文件架构

每个工作流步骤是独立的自包含文件：

```
bmad-prd/
├── create.md          # 创建意图
├── update.md          # 更新意图
├── validate.md        # 验证意图
├── prd-template.md    # PRD 模板
└── quality-rubric.md  # 质量评分标准
```

这种设计使得：
- 每个步骤可以独立维护和更新
- LLM 只需加载当前需要的步骤，节省上下文窗口
- 用户可以精确控制使用哪些步骤

### 7.3 门控机制

BMAD 在关键阶段设置了质量门控：

```
Analysis → [Product Brief Gate] → Planning → [PRD Gate] →
Solutioning → [Readiness Check Gate] → Implementation → [Code Review Gate]
```

每个门控都有明确的通过条件和质量标准。

### 7.4 Working Backwards

集成 Amazon 的 "Working Backwards" 方法论：

```
传统流程: 想法 → 技术方案 → 产品 → 市场
BMAD流程: 用户痛点 → PRFAQ → 产品简报 → PRD → 技术方案
```

从用户视角反向验证产品概念的可行性，避免"技术驱动"的产品失误。

## 8. 局限性分析

### 8.1 复杂度成本

| 维度 | 成本 |
|------|------|
| 学习曲线 | 需要理解四阶段流程、六角色系统、制品模板 |
| 制品维护 | 大量文档需要持续维护和更新 |
| 工具依赖 | 需要支持 skill 的 AI 工具（如 Claude Code） |
| 适用规模 | 对小型项目可能过重 |

### 8.2 灵活性约束

- **严格阶段流**: 必须按 Analysis → Planning → Solutioning → Implementation 顺序
- **文档格式**: 需要遵循标准模板，不能随意格式
- **角色绑定**: 每个阶段绑定特定 Agent 角色

### 8.3 实施门槛

- 需要安装和配置 BMAD 工具链
- 需要理解 Agile/Scrum 概念
- 需要团队对方法论达成共识

## 9. 设计启示

### 9.1 文件化上下文的力量

BMAD 证明了通过文件而非对话历史传递上下文可以：
- 解决 LLM 上下文窗口限制
- 提供可审计的决策链
- 支持跨会话的连续性

### 9.2 角色专业化的价值

不同阶段使用专业化的 Agent 角色，可以：
- 聚焦当前阶段的思维模式
- 避免单一 Agent 的认知偏差
- 提供多样化的视角

### 9.3 门控机制的重要性

质量门控防止了"带病前进"——在没有充分规划的情况下仓促编码。

---

**参考来源**:
- BMAD-METHOD 源码: `repos/bmad-method`
- Agent 定义: `repos/bmad-method/src/bmm-skills/`
- 工作流文件: `repos/bmad-method/src/bmm-skills/[1-4]-*/`
