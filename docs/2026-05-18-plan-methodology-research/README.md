# AI Plan 方法论调研 - Cursor Plan & BMAD-METHOD

## 调研概览

本调研分析了 AI 辅助开发中两种主流的 Plan-then-Execute 方法论：**Cursor Plan Mode** 和 **BMAD-METHOD**，产出可直接被 OpenSpec 消费的设计草稿和提案草稿。

**调研日期**: 2026-05-18
**调研范围**: Cursor Plan Mode、BMAD-METHOD v6

## 核心发现

### Cursor Plan Mode — 即时性轻量规划

- IDE 内嵌的 Plan-then-Execute 二阶段模式
- 核心流程：Research → Question → Plan → Review → Execute
- 优势：零学习成本、无缝集成 IDE、即时响应
- 限制：无持久化制品、无角色系统、适合中小型任务
- 社区反馈可防止约 90% 的架构问题

### BMAD-METHOD — 全周期结构化框架

- 四阶段生命周期：Analysis → Planning → Solutioning → Implementation
- 六角色 Agent 系统：Mary(BA)、Paige(TW)、John(PM)、Sally(UX)、Winston(SA)、Amelia(SSE)
- 文件化渐进式上下文传递：PRD → Architecture → Story → Code
- 优势：完整制品体系、质量门控、可审计
- 限制：学习曲线陡峭、对小型项目过重

### 融合方向：自适应分级规划

构建**自适应分级规划方法（Adaptive Tiered Planning）**：根据任务复杂度自动选择规划深度。

- Level 0: 即时执行（简单修改）
- Level 1: 快速规划（Cursor Plan 风格）
- Level 2: 结构化规划（轻量 BMAD）
- Level 3: 全周期规划（完整 BMAD）

## 文档索引

### 调研背景材料

| 文档 | 描述 | 阅读时间 |
|------|------|----------|
| [00-调研总览](./00-调研总览.md) | 总体索引和发现摘要 | 5 分钟 |
| [01-Cursor-Plan设计分析](./01-Cursor-Plan设计分析.md) | Cursor Plan Mode 完整分析 | 15-20 分钟 |
| [02-BMAD-METHOD设计分析](./02-BMAD-METHOD设计分析.md) | BMAD-METHOD 完整分析 | 20-25 分钟 |
| [03-方法论对比与融合](./03-方法论对比与融合.md) | 对比分析和融合设计方向 | 15-20 分钟 |

### OpenSpec 可消费产物

| 文档 | 描述 | OpenSpec 对接 |
|------|------|---------------|
| [proposal.md](./proposal.md) | 提案草稿 | `/opsx:propose` 输入 |
| [design.md](./design.md) | 设计草稿 | `/opsx:apply` 设计参考 |

## 参考来源

- Cursor Plan Mode: [cursor.com/blog/plan-mode](https://cursor.com/blog/plan-mode)
- BMAD-METHOD: `repos/bmad-method`
- Reddit 社区讨论和最佳实践

---

**调研人员**: AI Agent
**文档版本**: 1.0
**最后更新**: 2026-05-18
