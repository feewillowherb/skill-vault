## Context

BMAD-METHOD 是一个成熟的全周期结构化规划框架，提供完整的 epic 级需求分析能力。当前缺少标准化的制品存储和下游对接方式：

- BMAD 产出丰富（PRD、Architecture、Story 等），但缺乏面向 OpenSpec 的精简输出格式
- 不同 topic 的制品组织方式不统一
- 需要建立 BMAD → OpenSpec 的标准化工作流

## Goals / Non-Goals

**Goals:**

- 定义 Topic 文件夹规范：每个 epic 级需求对应一个 topic 文件夹，包含 `proposal.md` 和 `design.md`
- 建立制品格式约定：`proposal.md` 描述需求提案，`design.md` 保持概要级别
- 确保 Topic 文件夹可直接被 OpenSpec 导入消费

**Non-Goals:**

- 不重新定义 BMAD-METHOD 的规划流程
- 不替代 OpenSpec 的 propose/apply 工作流，而是为其提供标准化输入
- 不涉及具体的代码实现或执行引擎

## Decisions

### Decision 1: Topic 文件夹结构

```
topic-name/
├── proposal.md   ← Why / What Changes / Capabilities / Impact
└── design.md     ← Context / Goals / Decisions（概要级别）
```

**文件夹命名**: kebab-case 格式，反映 topic 内容（如 `plan-methodology-research`）

**理由**: 两个文件覆盖了 epic 级需求从提案到设计的核心信息，且直接映射到 OpenSpec 的 propose 和 apply 流程。

### Decision 2: proposal.md 格式

```
## Why           — 为什么需要这个变更
## What Changes  — 变更内容概述
## Capabilities  — 新增和修改的能力清单
## Impact        — 影响范围
```

**理由**: 这是 OpenSpec `/opsx:propose` 的标准输入格式，聚焦于"为什么要做"和"做什么"。

### Decision 3: design.md 保持概要级别

`design.md` 描述 Context、Goals/Non-Goals、关键 Decisions，但**不需要非常细节**。具体实现细节由 OpenSpec apply 阶段展开。

**理由**: 过度细节的 design.md 增加了 BMAD 阶段的成本，且后续 OpenSpec 流程会进一步细化，保持概要即可。

### Decision 4: OpenSpec 导入方式

直接将 topic 文件夹导入 OpenSpec，OpenSpec 自动识别 `proposal.md` 和 `design.md`，按序执行 propose → apply 流程。

**理由**: 文件夹作为导入单元，结构清晰、操作简单，无需额外的格式转换或中间步骤。

## Risks / Trade-offs

### design.md 概要 vs 细节的平衡

**风险**: 过于概要的 design.md 可能导致 OpenSpec apply 阶段信息不足。

**缓解**: design.md 应覆盖关键设计决策（Decisions），这些决策足以指导 apply 阶段的展开。具体的实施细节留给 apply 阶段。

### Topic 命名冲突

**风险**: 不同 epic 可能产生相同或相似的 topic 名称。

**缓解**: 使用足够具体的 kebab-case 命名，必要时添加日期或项目前缀。
