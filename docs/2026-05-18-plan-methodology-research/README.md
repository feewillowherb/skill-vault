# Cursor + OpenSpec + BMAD-METHOD 方法论

Epic 级需求：**BMAD 规划与拆分 → 用户衔接 → OpenSpec 分 change 执行**。任务清单（`tasks.md`）仅由 OpenSpec 管理。

## 工作流

```
Cursor
  ├─ BMAD skills (.agents/skills/)     →  PRD / 架构 / 多份 proposal + 概要 design
  ├─ 用户手工衔接                        →  各 slice → 目标项目 openspec/changes/<id>/
  └─ OpenSpec (/opsx:*)                 →  specs / design / tasks / apply / archive
```

## 文档索引

| 文档 | 说明 |
|------|------|
| [00-调研总览](./00-调研总览.md) | 入口、原则、导航 |
| [01-Cursor 中使用 BMAD-METHOD](./01-Cursor中使用BMAD-METHOD.md) | **Cursor 安装与 skill 使用** |
| [02-BMAD-METHOD 设计分析](./02-BMAD-METHOD设计分析.md) | BMAD 架构；Phase 4 在结合方案中跳过 |
| [03-Cursor-OpenSpec-BMAD 结合方案](./03-Cursor-OpenSpec-BMAD结合方案.md) | **完整结合流程与制品约定** |

### 示例制品（BMAD → OpenSpec 草稿）

| 文件 | 角色 |
|------|------|
| [proposal.md](./proposal.md) | 单个 slice 的 proposal 草稿示例 |
| [design.md](./design.md) | 概要 design，供 OpenSpec apply 参考（非 tasks） |

## 快速命令

```bash
# 业务项目根目录
npx bmad-method install --modules bmm --tools cursor
openspec init
```

Cursor 中：`bmad-help` → 规划与拆分 → 用户拷贝草稿 → `/opsx:propose` / `/opsx:apply`

## 参考仓库

- `repos/bmad-method`
- `repos/openspec`

---

**文档版本**: 4.0 · **最后更新**: 2026-05-20
