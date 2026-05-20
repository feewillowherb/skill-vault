# Cursor 中使用 BMAD-METHOD

本文说明如何在 **Cursor** 中正确安装、发现和使用 BMAD-METHOD，并与 OpenSpec 分工配合。

## 1. 安装

**前置**：Node.js ≥ 20.12、Python ≥ 3.10、[uv](https://docs.astral.sh/uv/)

在**目标业务项目**根目录执行（不要在仅作调研的 vault 文档目录里代替真实项目）：

```bash
npx bmad-method install --modules bmm --tools cursor
```

非交互示例：

```bash
npx bmad-method install --directory /path/to/your-app --modules bmm --tools cursor --yes
```

安装完成后，BMAD 将 skills 写入：

| 范围 | 路径 |
|------|------|
| 项目内 | `.agents/skills/<skill-name>/` |
| 用户全局（可选） | `~/.agents/skills/` |

Cursor 与 Codex、GitHub Copilot 等共用 `.agents/skills/` 跨工具标准（见 `repos/bmad-method/tools/installer/ide/platform-codes.yaml` 中 `cursor` 条目）。

## 2. Cursor 如何发现 BMAD skills

1. **Agent Skills**：Cursor 会索引 `.agents/skills/**/SKILL.md`，在对话中可按名称引用或让 Agent 自动选用。
2. **显式调用**：在提示中写明 skill 名，例如：
   - `使用 bmad-help，我刚完成架构，下一步做什么？`
   - `加载 bmad-create-architecture skill，基于当前 PRD 做跨服务架构`
3. **项目规则**：建议在业务项目 `AGENTS.md` 或 `.cursor/rules` 中加入（可复制）：

```markdown
## BMAD-METHOD（规划阶段）

- 本仓库已安装 BMAD（`npx bmad-method install --tools cursor`），skills 位于 `.agents/skills/`。
- Epic 级规划使用 BMAD：PRD、架构、Epic 拆分为多个 OpenSpec proposal 草稿。
- **禁止**在本流程中使用 BMAD Phase 4（`bmad-dev-story`、`bmad-sprint-planning`、`bmad-create-story` 等）生成任务清单；实施任务由 OpenSpec `tasks.md` 独占。
- 需要指引时优先调用 `bmad-help`。
```

## 3. 推荐使用的 BMAD skills（结合 OpenSpec 时）

| Skill / 工作流 | 用途 | OpenSpec 衔接 |
|----------------|------|----------------|
| `bmad-help` | 下一步该做什么 | 规划完成后提示用户转入 OpenSpec |
| `bmad-brainstorming` / `bmad-product-brief` | 可选：想法澄清 | 产出纳入 PRD 或 proposal 背景 |
| `bmad-prd` | PRD | 作为多个 proposal 的上游依据 |
| `bmad-create-ux-design` | UX 概要 | 写入 design 或 OpenSpec design 参考 |
| `bmad-create-architecture` | 技术架构、ADR、**跨前后端/多仓库**边界 | 产出 `design.md` 概要，**不写 tasks** |
| `bmad-create-epics-and-stories` | **仅用于 Epic → 多个可执行 proposal 切片** | 每个切片输出 `proposal.md` 草稿，**不输出 Story 任务列表** |
| `bmad-check-implementation-readiness` | 就绪检查（规划侧） | 确认可交给 OpenSpec，而非开始编码 |
| `bmad-generate-project-context` | `project-context.md` | 供 OpenSpec apply 时保持一致性 |

## 4. 明确不要使用的 BMAD 能力（交给 OpenSpec）

以下属于 BMAD **Phase 4 Implementation**，在本方法论中 **禁用**：

| 禁用项 | 原因 |
|--------|------|
| `bmad-sprint-planning` / `sprint-status.yaml` | 冲刺与任务状态由 OpenSpec change 管理 |
| `bmad-create-story` / Story 卡片中的 **Tasks** 列表 | 与 OpenSpec `tasks.md` 冲突 |
| `bmad-dev-story` | 代码实现由 `/opsx:apply` 驱动 |
| `bmad-code-review`（作为实施闭环） | 可在 OpenSpec apply 后按需单独使用，但不替代 OpenSpec tasks |

若在对话中 Agent 试图生成「开发任务清单」「按 Story 的 TODO」，应中止并改为：**「请只输出 OpenSpec 格式的 proposal 草稿，tasks 由 OpenSpec 生成。」**

## 5. 与 OpenSpec 在同一 Cursor 工作区共存

```bash
# 业务项目中同时存在：
.agents/skills/bmad-*/          # BMAD
openspec/                        # OpenSpec 初始化后
.claude/skills/openspec-*/       # 或 openspec init 为 Cursor 生成的等价路径
```

**阶段切换口令示例**：

- 规划：`bmad-help，把这个 epic 拆成 3 个可独立交付的 OpenSpec change，每个只要 proposal 和概要 design，不要 tasks。`
- 衔接：（用户复制文件到 `openspec/changes/...`）
- 执行：`/opsx:propose <change-id>` 或 `/opsx:apply`

## 6. 故障排查

| 现象 | 处理 |
|------|------|
| Cursor 找不到 BMAD skill | 确认 `--tools cursor` 安装；检查 `.agents/skills/` 是否存在 `SKILL.md` |
| Skill 与 OpenSpec 命令混淆 | 规划阶段只提 `bmad-*`；执行阶段只提 `/opsx:*` |
| 仍生成 Story/tasks | 在规则中重申「任务清单仅 OpenSpec」；换用 `bmad-create-epics-and-stories` 时限定输出格式 |

## 7. 参考

- BMAD 安装：[docs.bmad-method.org](https://docs.bmad-method.org)
- 源码：`repos/bmad-method`
- Cursor 平台 ID：`cursor` → `target_dir: .agents/skills`（`platform-codes.yaml`）

---

**文档版本**: 1.0  
**最后更新**: 2026-05-20
