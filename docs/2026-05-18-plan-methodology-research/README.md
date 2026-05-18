# Plan 方法论 — BMAD-METHOD 与 OpenSpec 工作流

## 工作流概览

```
BMAD-METHOD ──生成──→ topic 文件夹 ──导入──→ OpenSpec
                       ├── proposal.md        执行 propose/apply 流程
                       └── design.md
```

**三步流程**：

1. **BMAD-METHOD 生成制品**: 使用 BMAD-METHOD 工具对 epic 级需求进行规划分析，输出 `proposal.md`（提案）和 `design.md`（概要设计）
2. **存储到 topic 文件夹**: 将两个文件放入以 topic 命名的文件夹中（如本文件夹 `2026-05-18-plan-methodology-research`）
3. **导入 OpenSpec**: 直接将 topic 导入 OpenSpec，执行 `/opsx:propose` → `/opsx:apply` 流程

### 约定

- **proposal.md**: 描述需求动机、变更范围和影响，作为 OpenSpec `/opsx:propose` 的输入
- **design.md**: 概要级别的设计方案（不需要非常细节），作为 OpenSpec `/opsx:apply` 的参考
- **文件夹命名**: 使用 topic 名称，如 `plan-methodology-research`
- **OpenSpec 导入**: 直接导入 topic 文件夹即可启动 OpenSpec 工作流

## 文档索引

### 调研材料

| 文档 | 描述 |
|------|------|
| [00-调研总览](./00-调研总览.md) | BMAD-METHOD 分析与工作流概述 |
| [02-BMAD-METHOD设计分析](./02-BMAD-METHOD设计分析.md) | BMAD-METHOD 完整分析 |

### 工作流设计

| 文档 | 描述 |
|------|------|
| [03-工作流设计](./03-方法论对比与融合.md) | BMAD → Topic → OpenSpec 工作流设计 |

### OpenSpec 制品

| 文档 | 描述 | OpenSpec 对接 |
|------|------|---------------|
| [proposal.md](./proposal.md) | 提案 | `/opsx:propose` 输入 |
| [design.md](./design.md) | 概要设计 | `/opsx:apply` 参考 |

## 参考来源

- BMAD-METHOD: `repos/bmad-method`

---

**文档版本**: 3.0
**最后更新**: 2026-05-18
