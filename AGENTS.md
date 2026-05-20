# CodeRef Vault Guide

This vault follows the standard CodeRef root layout.

- `docs/` stores research notes, explanations, and walkthroughs.
- `repos/` stores Git submodules that point to source repositories or samples.
- `index.yaml` is the canonical metadata entry for this vault root.

Repositories under `repos/` should be maintained through Git submodules rather than copied directly into the vault root.

The canonical `index.yaml` fields are:

- `schemaVersion`
- `type`
- `title`
- `docsRoot`
- `reposRoot`

Keep this structure stable so assistants and tools can understand the vault quickly.

## Output Language

- 默认输出语言为中文。专用名词（如函数名、命名空间、NuGet 包名、API 名称等）保留原文不翻译。

## Research Output Format

每次调研产出统一放置在 `docs/` 下的独立文件夹中，而非散落为多个独立文件。文件夹命名格式：

```
docs/<YYYY-MM-DD>-<提案或主题名称>/
```

示例：

```
docs/2026-01-01-topic-name/
├── 00-调研总览.md
├── 01-使用指南.md
├── 02-技术参考.md
├── 03-快速参考.md
└── ...
```

规则：

- 文件夹名称由日期和提案名称组成，使用连字符分隔。
- 文件夹内的文档按编号排序，编号从 `00` 开始。
- 每个调研文件夹应包含一个 `00-调研总览.md` 作为入口索引。

## Epic 规划：BMAD-METHOD + OpenSpec（在 Cursor 中）

源码参考：`repos/bmad-method`、`repos/openspec`。完整结合方案见 `docs/2026-05-18-plan-methodology-research/`（入口 [00-调研总览.md](docs/2026-05-18-plan-methodology-research/00-调研总览.md)）。

**分工**：

- **BMAD**（业务项目内安装）：`npx bmad-method install --modules bmm --tools cursor` → skills 在 `.agents/skills/`。用于 Epic 规划、架构、拆成多份 `proposal.md` / 概要 `design.md`。**禁止**生成 `tasks.md` 或使用 Phase 4 实施工作流（`bmad-dev-story` 等）。
- **用户**：将每个 BMAD slice 手工导入目标仓库的 OpenSpec change。
- **OpenSpec**：`openspec init` 后使用 `/opsx:propose`、`/opsx:apply`；**`tasks.md` 仅由 OpenSpec 维护**。

规划阶段可调用 `bmad-help`；需要 Cursor 操作说明时阅读 `docs/2026-05-18-plan-methodology-research/01-Cursor中使用BMAD-METHOD.md`。
