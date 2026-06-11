# Reasoning-Annotate Skill 设计草案

## Skill 概述

`reasoning-annotate` 是一个独立安装的 AI coding skill，用于在 OpenSpec 提案生成过程中标注高价值 AI 推理内容。它以非阻断方式运行，不修改 OpenSpec 流程，纯粹附加标注。分类数据存储在 YAML 配置文件中，与 OpenSpec 的 `config.yaml` 风格一致。

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
description: Annotate high-value AI reasoning in OpenSpec proposal and design artifacts using a user-AI co-maintained YAML classification table. Use when generating or reviewing OpenSpec proposals or designs to highlight reasoning that matches subscribed categories. Non-blocking — annotations only, never halts workflow.
---

You operate under reasoning annotation. When OpenSpec proposal artifacts are generated or reviewed, identify and annotate high-value AI reasoning content that matches user-subscribed categories.

## Classification table

A YAML file defines what counts as high-value reasoning. Load the table at the start of each annotation pass.

### Loading order (first found wins)

1. **Project level**: `openspec/reasoning-categories.yaml` (alongside OpenSpec config.yaml)
2. **Global level**: `~/.openspec/reasoning-categories.yaml`
3. **Built-in default**: if neither file exists, use the embedded default categories

### Reading the YAML

The YAML file has this structure:

\`\`\`yaml
domains:
  技术:
    <category_id>:
      name: <display name>
      description: <matching criteria for AI>
      priority: high | medium | low
      subscribed: true | false
  业务:
    <category_id>:
      name: <display name>
      description: <matching criteria for AI>
      priority: high | medium | low
      subscribed: true | false
  通用:
    <category_id>:
      name: <display name>
      description: <matching criteria for AI>
      priority: high | medium | low
      subscribed: true | false
\`\`\`

- Top-level `domains` groups categories by domain
- Each domain is a flat map of `category_id` → category definition
- `subscribed: false` categories are loaded but **inactive** — they appear in the reference but do not trigger annotations
- `priority` controls annotation emphasis: `high`, `medium`, `low`

### If no YAML exists

Output a one-time notice: "No reasoning-categories.yaml found. Using built-in defaults. Create `openspec/reasoning-categories.yaml` to customize." Then proceed with built-in defaults.

## Annotation process

### Trigger context

Activate when any of these occur:
- An OpenSpec `proposal.md` is generated or updated
- An OpenSpec `design.md` is generated or updated
- The user explicitly requests reasoning review: `/reasoning-annotate` or "annotate reasoning"

Do NOT activate during code implementation (`/opsx:apply` task execution) or on `spec.md` / `tasks.md`. This skill targets proposal.md and design.md only.

### Step 1: Scan the artifact

Read the generated artifact (proposal.md or design.md). Identify passages where AI reasoning is visible:

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

\`\`\`markdown
[🏷️ <category_name>] <priority>
> *Reasoning location*: "<section or paragraph reference>"
> *Matched content*: "<brief excerpt or paraphrase>"
> *Annotation*: <why this is high-value reasoning in context>
\`\`\`

Priority formatting:
- `high`: `[🏷️ <category_name>] ⬆️ HIGH`
- `medium`: `[🏷️ <category_name>] ◉ MEDIUM`
- `low`: `[🏷️ <category_name>] ○ LOW`

### Step 4: Append annotations to the artifact

**Directly append** a `## Reasoning Annotations` section to the end of the artifact file (proposal.md or design.md). This happens automatically — no user confirmation needed.

If the section already exists (from a previous annotation pass), **replace it entirely**.

Append format:

```markdown
<!-- reasoning-annotate: auto-generated — <YAML source path> -->
## Reasoning Annotations

**Classification source**: `<file path of the YAML used>`
**Categories active**: <count of subscribed=true categories> / <total>
**Annotations found**: <count>

### Summary by Domain
| Domain | Categories matched | Annotation count |
|--------|-------------------|-----------------|
| 技术   |                   |                 |
| 业务   |                   |                 |
| 通用   |                   |                 |

### Coverage metrics
- Reasoning passages detected: <count>
- Matched to subscribed categories: <count>
- Unmatched passages: <count>

---

[🏷️ <category_name>] <priority>
> *Location*: "<section reference>"
> *Content*: "<excerpt>"
> *Annotation*: <why this is high-value>

<!-- /reasoning-annotate -->
```

### Step 5: Brief in-session notification

After appending, output a single-line summary in the session:

> "Appended <count> reasoning annotations to `<artifact-name>` (source: `<YAML path>`, coverage: <percentage>%)"

Nothing more. Do not dump the full annotation list into the session — the artifact is the single source of truth.

## Built-in default categories

When no YAML is found, use these defaults:

\`\`\`yaml
domains:
  技术:
    arch-decision:
      name: 架构决策
      description: 涉及系统架构选择和权衡的推理
      priority: high
      subscribed: true
    security:
      name: 安全考量
      description: 安全相关的设计推理
      priority: high
      subscribed: true
    perf-impact:
      name: 性能影响
      description: 影响性能的设计选择推理
      priority: medium
      subscribed: true
    data-model:
      name: 数据模型
      description: 数据结构设计相关推理
      priority: medium
      subscribed: true
    integration:
      name: 集成风险
      description: 外部依赖和集成点相关推理
      priority: medium
      subscribed: true
    migration:
      name: 迁移策略
      description: 数据或系统迁移相关推理
      priority: low
      subscribed: true
    monitoring:
      name: 可观测性
      description: 日志、监控、告警相关推理
      priority: low
      subscribed: false
  业务:
    biz-rule:
      name: 业务规则
      description: 涉及业务流程、领域逻辑的推理
      priority: high
      subscribed: true
    ux-assumption:
      name: UX假设
      description: 关于用户体验的假设推理
      priority: medium
      subscribed: false
  通用:
    scope-decision:
      name: 范围决策
      description: 关于功能范围边界的推理
      priority: medium
      subscribed: true
\`\`\`

## Behavior guardrails

### Must do
- Always load the YAML before annotating
- Only annotate reasoning that is actually present in the artifact
- Respect `subscribed` field — inactive categories must not produce annotations
- Always append the `## Reasoning Annotations` section to the artifact file (proposal.md or design.md)
- Report which YAML file was used as the classification source
- Output a brief in-session notification after appending

### Must not do
- Do NOT block or delay the OpenSpec propose workflow
- Do NOT modify existing artifact content — only append the annotation section at the end
- Do NOT annotate during code implementation (apply phase), on spec.md, or on tasks.md
- Do NOT invent reasoning that is not present in the artifact
- Do NOT dump the full annotation list into the session — the artifact is the source of truth
- Do NOT replace or override guess-governance — this skill is complementary

### Relationship with guess-governance
- guess-governance triages assumptions → reasoning-annotate labels reasoning
- They are independent; having one does not require the other
- If both are active, they operate on the same artifact without conflict
- guess-governance may block; reasoning-annotate never blocks

## YAML maintenance

### User-initiated updates
The user may edit the YAML file directly at any time. The skill reads the latest version on each activation.

### AI-suggested updates
When the skill detects reasoning that does not fit any subscribed category but seems valuable:

> "⚠️ Unmatched high-value reasoning detected in `<section>` that does not fit any subscribed category. Consider adding a new category or enabling an inactive one. Run `/reasoning-annotate suggest` to see recommendations."

If the user runs `/reasoning-annotate suggest`:

1. Collect all unmatched passages from recent annotation passes
2. Analyze patterns — do they cluster around a theme?
3. If a clear pattern emerges, propose a new category entry to append under the appropriate domain
4. Present the suggestion for user approval before any file modification
5. If approved, append the entry to the project-level YAML (create the file if needed)

### Never auto-modify the YAML without explicit user approval.

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
| 触发条件 | every propose/design/apply/review | proposal.md / design.md 生成时 |
| 分类体系 | 内置三级 (L1/L2/L3) | YAML 外部分类表 |
| 分类存储 | 内置固定 | YAML 文件，按 domain 分组 |
| 量化指标 | Guess Count/Ratio/Risk | Detection/Coverage/Spread |
| 行为护栏 | Do/Don't | Must do / Must not do |
| 产物模板 | 嵌入 proposal.md section | 直接追加 `## Reasoning Annotations` section |
| 阻断逻辑 | Gates + When blocked | 无阻断 |
| 维护机制 | 内置固定 | YAML 用户/AI 共同维护 |

### YAML vs CSV 选型理由

| 维度 | CSV | YAML（选定） |
|------|-----|-------------|
| 与 OpenSpec 一致性 | ❌ config.yaml 是 YAML | ✅ 同格式风格 |
| 嵌套分组 | ❌ 需要额外解析 | ✅ 按 domain 自然分组 |
| 注释支持 | ✅ `#` 行注释 | ✅ `#` 行注释 |
| AI 解析可靠性 | ⚠️ 需处理逗号/换行转义 | ✅ 结构化，无需转义 |
| 人类可读性 | ⚠️ 长行难以阅读 | ✅ 层级清晰 |
| 字段扩展性 | ⚠️ 需改 header | ✅ 直接添加字段 |

### YAML Frontmatter 说明

```yaml
name: reasoning-annotate              # skill 唯一标识
description: ...                       # auto-trigger 关键词匹配
                                     # 包含：annotate, reasoning, OpenSpec, proposals
```

description 被优化为 auto-trigger 格式，当用户的对话涉及"标注推理"、"OpenSpec 提案"等关键词时自动激活。

---

**文档版本**: 1.2
**最后更新**: 2026-06-11
