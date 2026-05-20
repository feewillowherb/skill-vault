## Context

BMAD-METHOD 提供 epic 级规划与跨项目架构能力；OpenSpec 提供 change 级 specs 与 **tasks** 驱动的实现循环。二者需在 Cursor 中共存，且由用户手工衔接，避免双套任务体系冲突。

本 `design.md` 为 **BMAD 概要设计**，供后续在业务仓库中执行 OpenSpec `/opsx:apply` 时参考。**不包含实现任务列表。**

## Goals / Non-Goals

**Goals:**

- BMAD 负责：PRD、架构、Epic → 多 `proposal.md` 草稿、跨前后端/多仓库概要 design、`project-context.md`
- 用户负责：将每个 slice 导入对应项目的 OpenSpec change
- OpenSpec 负责：`specs`、`tasks.md`、`/opsx:apply`、`/opsx:archive`
- Cursor：统一承载 `.agents/skills/`（BMAD）与 OpenSpec 命令

**Non-Goals:**

- BMAD 自动生成 OpenSpec change 或调用 `openspec` CLI
- BMAD 输出 `tasks.md`、Story 任务列表、sprint-status 驱动开发
- 用 BMAD Phase 4（`bmad-dev-story` 等）替代 `/opsx:apply`

## Decisions

### Decision 1: 手工衔接，无自动化管道

用户从 `planning/<epic>/slices/<name>/` 拷贝 `proposal.md`（及可选 `design.md`）到目标仓库，再执行 OpenSpec。理由：Epic 常跨多个 repo，自动化导入易错绑仓库。

### Decision 2: tasks 独占 OpenSpec

| 制品 | BMAD | OpenSpec |
|------|------|----------|
| proposal 草稿 | ✅ | 正式版 |
| 概要 design | ✅ | 可合并扩展 |
| **tasks.md** | ❌ | ✅ |

### Decision 3: Cursor 安装 BMAD 至 `.agents/skills/`

使用 `npx bmad-method install --tools cursor`（见 `platform-codes.yaml`）。项目 `AGENTS.md` 中声明规划/执行阶段分工，减少 Agent 误用 `bmad-dev-story`。

### Decision 4: 多项目 Epic 在架构阶段划界

`bmad-create-architecture` 产出系统边界与 repo 映射；每个 slice 的 OpenSpec change 落在单一目标仓库。

## Risks / Trade-offs

**风险**: 用户跳过衔接，直接在 BMAD 对话里要求写代码。  
**缓解**: 规则与 `bmad-help` 提示中强调「规划结束后使用 `/opsx:propose`」。

**风险**: 概要 design 过薄导致 OpenSpec apply 信息不足。  
**缓解**: design 至少包含 Decisions；细节由 OpenSpec 在 propose 链路中展开，**仍不**在 BMAD 中写 tasks。
