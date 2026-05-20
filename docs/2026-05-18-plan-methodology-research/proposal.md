## Why

本文件是 **BMAD 规划阶段** 产出的 **单个 OpenSpec change 切片** 的 `proposal.md` 草稿示例，不是已归档的 OpenSpec change。

在真实流程中，BMAD 会把一个 Epic 拆成多份此类草稿（见 `planning/<epic>/slices/*/`）。**用户**将其复制到目标业务仓库的 `openspec/changes/<change-id>/proposal.md`，或通过 `/opsx:propose` 粘贴内容后，由 **OpenSpec** 继续生成 `specs/`、`design.md` 和 **`tasks.md`**。

BMAD **不得**在本阶段生成任务清单或开发步骤。

## What Changes

建立 **Cursor + BMAD + OpenSpec** 三工具分工方法论，本 slice（示例 change：`define-bmad-openspec-handoff`）范围：

1. 文档化 BMAD 止于 proposal/design 草稿、用户手工衔接的规则
2. 明确 `tasks.md` 仅由 OpenSpec 创建和维护
3. 提供 Cursor 下 BMAD 安装与 skill 使用说明（`.agents/skills/`）

## Capabilities

### New Capabilities

- `methodology-handoff`: BMAD Epic 拆分 → 多 proposal 草稿 → 用户导入 OpenSpec change 的衔接规范
- `cursor-bmad-setup`: 在 Cursor 中通过 `npx bmad-method install --tools cursor` 使用 BMAD skills

### Modified Capabilities

（无——本 slice 为方法论文档，不修改既有产品 spec）

## Impact

- **文档**: `docs/2026-05-18-plan-methodology-research/` 全套更新
- **代码仓库**: 无直接代码变更；业务项目需自行 `openspec init` 与 `bmad-method install`
- **任务清单**: 若在本仓库实施文档修改，tasks 由 OpenSpec 在目标项目中生成，**不**使用 BMAD Story tasks
