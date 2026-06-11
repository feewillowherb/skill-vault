# Reasoning-Annotate Skill 设计草案

## Skill 概述

`reasoning-annotate` 是一个独立安装的 AI coding skill，用于在 OpenSpec 提案生成过程中标注高价值 AI 推理内容。它以非阻断方式运行，不修改 OpenSpec 流程，纯粹附加标注。

## 设计参考

以 `guess-governance` trait 为参考模板，借鉴其：
- YAML frontmatter 结构
- 分类体系设计模式
- 量化指标思路
- 行为护栏格式
- 产物内嵌模板

关键区别：guess-governance 可阻断流程；reasoning-annotate **永远不阻断**。

---

## SKILL.md 完整草案

```markdown
---
name: reasoning-annotate
description: Annotate high-value AI reasoning in OpenSpec proposals using a user-AI co-maintained classification table. Use when generating or reviewing OpenSpec proposals, designs, or specs to highlight reasoning that matches subscribed categories. Non-blocking — annotations only, never halts workflow.
---

You operate under reasoning annotation. When OpenSpec proposal artifacts are generated or reviewed, identify and annotate high-value AI reasoning content that matches user-subscribed categories.

## Classification table

A CSV file defines what counts as high-value reasoning. Load the table at the start of each annotation pass.

### Loading order (first found wins)

1. **Project level**: `.openspec/reasoning-categories.csv` (in the project root or openspec planning home)
2. **Global level**: `~/.openspec/reasoning-categories.csv`
3. **Built-in default**: if neither file exists, use the embedded default categories

### Reading the CSV

- First row = header. Required columns: `category_id`, `category_name`, `domain`, `description`, `priority`, `subscribed`
- Rows where `subscribed` is not `true` are loaded but **inactive** — they appear in the classification reference but do not trigger annotations
- The `domain` column groups categories: `技术` (technical), `业务` (business), `通用` (general)
- The `priority` column controls annotation emphasis: `high`, `medium`, `low`

### If no CSV exists

Output a one-time notice: "No reasoning-categories.csv found. Using built-in defaults. Create `.openspec/reasoning-categories.csv` to customize." Then proceed with built-in defaults.

## Annotation process

### Trigger context

Activate when any of these occur:
- An OpenSpec `proposal.md` is generated or updated
- An OpenSpec `design.md` is generated or updated
- The user explicitly requests reasoning review: `/reasoning-annotate` or "annotate reasoning"

Do NOT activate during code implementation (`/opsx:apply` task execution). This skill targets planning artifacts, not code.

### Step 1: Scan the artifact

Read the generated artifact (proposal.md, design.md, or spec.md). Identify passages where AI reasoning is visible:

**What counts as visible AI reasoning**:
- Trade-off explanations (choosing A over B with justification)
- Assumption statements (explicit or implicit "we assume X")
- Risk acknowledgments (noting what could go wrong)
- Scope boundary decisions (why something is in/out of scope)
- Architecture choices with rationale
- Business rule interpretations
- Security/performance/reliability considerations
- Dependencies and integration assumptions
- Novel approaches or non-obvious solutions

### Step 2: Match against subscribed categories

For each identified reasoning passage, check if it matches any **subscribed** category:

- Match by `description` semantics — the passage discusses what the category describes
- One passage can match multiple categories
- One category can match multiple passages

### Step 3: Generate annotations

For each match, produce an annotation entry:

```markdown
[🏷️ <category_name>] <priority>
> *Reasoning location*: "<section or paragraph reference>"
> *Matched content*: "<brief excerpt or paraphrase>"
> *Annotation*: <why this is high-value reasoning in context>
```

Priority formatting:
- `high`: `[🏷️ <category_name>] ⬆️ HIGH`
- `medium`: `[🏷️ <category_name>] ◉ MEDIUM`
- `low`: `[🏷️ <category_name>] ○ LOW`

### Step 4: Output annotations

**Always output in-session summary.** Format:

```markdown
## Reasoning Annotations — <change-name>/<artifact>

**Classification source**: <file path of the CSV used>
**Categories active**: <count of subscribed=true categories>
**Annotations found**: <count>

<annotations in order of appearance in the artifact>

### Summary by Domain
| Domain | Categories matched | Annotation count |
|--------|-------------------|-----------------|
| 技术   |                   |                 |
| 业务   |                   |                 |
| 通用   |                   |                 |

### Coverage metrics
- Reasoning passages detected: <count>
- Matched to subscribed categories: <count>
- Unmatched passages: <count> (below subscription threshold or no category fit)
```

### Step 5: Optional proposal.md appendix

After the in-session output, offer (once, non-blocking):

> "Append these annotations to the artifact as a `## Reasoning Annotations` section? (y/n)"

If the user confirms, append the annotation block to the end of the artifact file. If the section already exists, replace it entirely.

## Built-in default categories

When no CSV is found, use these defaults:

| category_id | category_name | domain | description | priority | subscribed |
|-------------|--------------|--------|-------------|----------|------------|
| arch-decision | 架构决策 | 技术 | 涉及系统架构选择和权衡的推理 | high | true |
| biz-rule | 业务规则 | 业务 | 涉及业务流程、领域逻辑的推理 | high | true |
| security | 安全考量 | 技术 | 安全相关的设计推理 | high | true |
| perf-impact | 性能影响 | 技术 | 影响性能的设计选择推理 | medium | true |
| data-model | 数据模型 | 技术 | 数据结构设计相关推理 | medium | true |
| integration | 集成风险 | 技术 | 外部依赖和集成点相关推理 | medium | true |
| ux-assumption | UX假设 | 业务 | 关于用户体验的假设推理 | medium | false |
| scope-decision | 范围决策 | 通用 | 关于功能范围边界的推理 | medium | true |
| migration | 迁移策略 | 技术 | 数据或系统迁移相关推理 | low | true |
| monitoring | 可观测性 | 技术 | 日志、监控、告警相关推理 | low | false |

## Behavior guardrails

### Must do
- Always load the CSV before annotating
- Only annotate reasoning that is actually present in the artifact
- Respect `subscribed` column — inactive categories must not produce annotations
- Report which CSV file was used as the classification source
- Offer to persist annotations (never auto-write)

### Must not do
- Do NOT block or delay the OpenSpec propose workflow
- Do NOT modify the artifact structure beyond the optional appendix
- Do NOT remove or alter existing artifact content
- Do NOT annotate during code implementation (apply phase)
- Do NOT invent reasoning that is not present in the artifact
- Do NOT force annotations into the artifact without user consent
- Do NOT replace or override guess-governance — this skill is complementary

### Relationship with guess-governance
- guess-governance triages assumptions → reasoning-annotate labels reasoning
- They are independent; having one does not require the other
- If both are active, they operate on the same artifact without conflict
- guess-governance may block; reasoning-annotate never blocks

## CSV maintenance

### User-initiated updates
The user may edit the CSV directly at any time. The skill reads the latest version on each activation.

### AI-suggested updates
When the skill detects reasoning that does not fit any subscribed category but seems valuable:

> "⚠️ Unmatched high-value reasoning detected in `<section>` that does not fit any subscribed category. Consider adding a new category or enabling an inactive one. Run `/reasoning-annotate suggest` to see recommendations."

If the user runs `/reasoning-annotate suggest`:

1. Collect all unmatched passages from recent annotation passes
2. Analyze patterns — do they cluster around a theme?
3. If a clear pattern emerges, propose a new CSV row:
   ```csv
   <suggested_category_id>,<suggested_name>,<domain>,<description>,<priority>,true
   ```
4. Present the suggestion for user approval before any file modification
5. If approved, append the row to the project-level CSV (create the file if needed)

### Never auto-modify the CSV without explicit user approval.

## Annotation quality metrics

Track internally for calibration (output in the summary when available):

| Metric | Description |
|--------|-------------|
| Detection count | Total reasoning passages identified |
| Coverage rate | Matched passages ÷ total detected |
| Category spread | How many distinct categories were matched |
| Unmatched rate | Unmatched passages ÷ total detected |
| Hot categories | Top 3 most frequently matched categories |

These metrics help the user calibrate their subscription — if coverage is low, they may need more categories; if a category never matches, it may be too narrow.
```

---

## 设计说明

### 与 guess-governance 的结构对比

| 模块 | guess-governance | reasoning-annotate |
|------|-----------------|-------------------|
| 触发条件 | every propose/design/apply/review | proposal/design/spec 生成时 |
| 分类体系 | 内置三级 (L1/L2/L3) | CSV 外部分类表 |
| 量化指标 | Guess Count/Ratio/Risk | Detection/Coverage/Spread |
| 行为护栏 | Do/Don't | Must do / Must not do |
| 产物模板 | 嵌入 proposal.md section | 可选追加 + 会话摘要 |
| 阻断逻辑 | Gates + When blocked | 无阻断 |
| 维护机制 | 内置固定 | CSV 用户/AI 共同维护 |

### YAML Frontmatter 说明

```yaml
name: reasoning-annotate              # skill 唯一标识
description: ...                       # auto-trigger 关键词匹配
                                     # 包含：annotate, reasoning, OpenSpec, proposals
```

description 被优化为 auto-trigger 格式，当用户的对话涉及"标注推理"、"OpenSpec 提案"等关键词时自动激活。

---

**文档版本**: 1.0
**最后更新**: 2026-06-11
