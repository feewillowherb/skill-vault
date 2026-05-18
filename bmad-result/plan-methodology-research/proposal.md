## Why

当前使用 BMAD-METHOD 进行 epic 级需求规划后，产出的制品（PRD、Architecture、Story 等）需要手动整理才能被下游工具消费。缺乏一个标准化的制品格式和存储约定，导致：

- 制品格式不统一，不同 topic 的文档结构不一致
- 缺少与 OpenSpec 的标准化对接方式
- 人工整理制品增加了不必要的流程开销

需要建立一套 **BMAD-METHOD 生成 → Topic 文件夹存储 → OpenSpec 消费** 的标准化工作流。

## What Changes

建立 BMAD-METHOD 与 OpenSpec 之间的标准化工作流，核心变化：

1. **Topic 文件夹约定**: 每个 epic 级需求对应一个以 topic 命名的文件夹，包含 `proposal.md` 和 `design.md`
2. **制品格式标准化**: `proposal.md` 作为提案（Why/What/Capabilities/Impact），`design.md` 作为概要设计（Context/Goals/Decisions），保持简洁
3. **OpenSpec 直接导入**: Topic 文件夹作为 OpenSpec 的导入单元，无需额外格式转换

## Capabilities

### New Capabilities

- `topic-management`: Topic 文件夹的创建、命名和组织规范，每个 topic 包含标准的 proposal.md 和 design.md
- `bmad-artifact-generation`: 使用 BMAD-METHOD 生成 epic 级需求的标准制品，输出符合 topic 约定的 proposal 和概要设计
- `openspec-import`: 将 topic 文件夹直接导入 OpenSpec，自动识别 proposal.md 和 design.md 执行 propose → apply 流程

### Modified Capabilities

（无现有 capability 需要修改——这是全新的工作流设计）

## Impact

- **工作流标准化**: 从 BMAD 规划到 OpenSpec 执行的流程有了清晰的制品规范和存储约定
- **制品一致性**: 所有 topic 遵循统一的文件夹结构和文件格式
- **design 保持概要**: design.md 聚焦关键设计决策，不过度深入细节，降低编写成本
- **OpenSpec 集成**: Topic 文件夹可直接导入 OpenSpec，无需手动整理或格式转换
