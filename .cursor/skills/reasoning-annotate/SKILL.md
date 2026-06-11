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

```yaml
domains:
  技术:
    <category_id>:
      name: <display name>
      description: <matching criteria for AI>
      priority: high | medium | low
      subscribed: true | false
      artifacts: proposal | design | both
      guess: true | false
  业务:
    <category_id>:
      name: <display name>
      description: <matching criteria for AI>
      priority: high | medium | low
      subscribed: true | false
      artifacts: proposal | design | both
      guess: true | false
  通用:
    <category_id>:
      name: <display name>
      description: <matching criteria for AI>
      priority: high | medium | low
      subscribed: true | false
      artifacts: proposal | design | both
      guess: true | false
```

- Top-level `domains` groups categories by domain
- Each domain is a flat map of `category_id` → category definition
- `subscribed: false` categories are loaded but **inactive** — they appear in the reference but do not trigger annotations
- `priority` controls annotation emphasis: `high`, `medium`, `low`
- `artifacts` controls which artifact types this category applies to:
  - `proposal` — only annotate proposal.md
  - `design` — only annotate design.md
  - `both` — annotate both (default if field is absent)
- `guess: true` marks categories where the annotation should detect and label the reasoning's fact/assumption basis

### If no YAML exists

Output a one-time notice: "No reasoning-categories.yaml found. Using built-in defaults. Create `openspec/reasoning-categories.yaml` to customize." Then proceed with built-in defaults.

## Annotation process

### Trigger context

Activate when any of these occur:
- An OpenSpec `proposal.md` is generated or updated
- An OpenSpec `design.md` is generated or updated
- The user explicitly requests reasoning review: `/reasoning-annotate` or "annotate reasoning"

Do NOT activate during code implementation (`/opsx:apply` task execution) or on `spec.md` / `tasks.md`. This skill targets proposal.md and design.md only.

### Step 1: Determine artifact type and filter categories

Before scanning, identify which artifact is being annotated and filter the active category list:

- If annotating **proposal.md**: keep categories where `artifacts` is `proposal` or `both`
- If annotating **design.md**: keep categories where `artifacts` is `design` or `both`

This ensures proposal-only categories (e.g., capability-tradeoff, stakeholder-impact) never fire on design.md, and design-only categories (e.g., interface-decision, implementation-strategy, tech-tradeoff) never fire on proposal.md.

### Step 2: Scan the artifact with the appropriate lens

Read the generated artifact. Identify passages where AI reasoning is visible. Use the artifact-type-aware scan focus:

**proposal.md scan focus — business and scope reasoning**:
- Why this change is needed (problem framing)
- What capabilities to add/modify/remove and why
- Scope boundaries and what is explicitly out of scope
- Business impact and stakeholder effects
- Risk acknowledgments at the product level
- Capability trade-offs (MVP vs full, which tier)

**design.md scan focus — technical and implementation reasoning**:
- Architecture choices with rationale (sync vs async, SQL vs NoSQL, monolith vs microservice)
- Interface/API design decisions (function signatures, data protocols, module contracts)
- Implementation strategy choices (algorithm selection, data flow, error handling approach)
- Technical trade-offs with explicit comparison (A vs B with justification)
- Security/performance/reliability design decisions
- Integration patterns and dependency management
- Data model design and migration strategy

**Guess-aware scan (when category has `guess: true`)**:
- For each reasoning passage, additionally determine: is this reasoning grounded in a **Fact** (confirmed requirement, existing code, documented constraint) or an **Assumption** (inferred, not yet validated)?
- If the artifact contains a guess-governance `Assumptions` table (A-01, A-02…), cross-reference: does this reasoning correspond to any listed assumption?
- Label the guess basis in the annotation

### Step 3: Match against filtered categories

For each identified reasoning passage, check if it matches any **active and artifact-filtered** category:

- Match by `description` semantics — the passage discusses what the category describes
- One passage can match multiple categories
- One category can match multiple passages

### Step 4: Generate annotations

For each match, produce an annotation entry. Format differs by guess awareness:

**Standard annotation** (category has `guess: false`):

```markdown
### <category_id> <priority>
**Location**: "<section reference>"
> <excerpt>
```

**Guess-aware annotation** (category has `guess: true`):

```markdown
### <category_id> <priority>
**Location**: "<section reference>"
**Basis**: Fact | Assumption [<A-ID>] | Mixed
> <excerpt>
```

Priority formatting:
- `high`: `⬆️ HIGH`
- `medium`: `◉ MEDIUM`
- `low`: `○ LOW`

**Basis field values**:
- `Fact` — reasoning is grounded in confirmed requirements, existing code, or documented constraints
- `Assumption` — reasoning relies on unvalidated inference; include the assumption ID if guess-governance is active (e.g., `Assumption [A-02]`)
- `Mixed` — reasoning combines facts and assumptions

### Step 5: Append annotations to the artifact

**Directly append** a `## Reasoning Annotations` section to the end of the artifact file. This happens automatically — no user confirmation needed.

If the section already exists (from a previous annotation pass), **replace it entirely**.

Append format:

```markdown
<!-- reasoning-annotate: auto-generated — <YAML source path> -->
## Reasoning Annotations

**Classification source**: `<file path of the YAML used>`
**Artifact type**: proposal | design
**Categories active**: <count of applicable categories> / <total>
**Annotations found**: <count>

### Summary by Domain
| Domain | Categories matched | Annotation count |
|--------|-------------------|-----------------|
| 技术   |                   |                 |
| 业务   |                   |                 |
| 通用   |                   |                 |

### Guess awareness
| Metric | Value |
|--------|-------|
| Fact-based reasoning | <count> |
| Assumption-based reasoning | <count> |
| Mixed reasoning | <count> |
| Linked assumptions | <comma-separated A-IDs, or "none"> |

### Coverage metrics
- Reasoning passages detected: <count>
- Matched to active categories: <count>
- Unmatched passages: <count>

---

<annotation entries>

<!-- /reasoning-annotate -->
```

### Step 6: Brief in-session notification

After appending, output a single-line summary in the session:

> "Appended <count> annotations to `<artifact-name>` (<fact-based>/<assumption-based> split, source: `<YAML path>`)"

Nothing more. Do not dump the full annotation list into the session — the artifact is the single source of truth.

## Built-in default categories

When no YAML is found, use these defaults:

```yaml
domains:
  技术:
    arch-decision:
      name: 架构决策
      description: 涉及系统架构选择和权衡的推理
      priority: high
      subscribed: true
      artifacts: both
      guess: true
    security:
      name: 安全考量
      description: 安全相关的设计推理
      priority: high
      subscribed: true
      artifacts: both
      guess: true
    perf-impact:
      name: 性能影响
      description: 影响性能的设计选择推理
      priority: medium
      subscribed: true
      artifacts: both
      guess: true
    data-model:
      name: 数据模型
      description: 数据结构设计相关推理
      priority: medium
      subscribed: true
      artifacts: both
      guess: true
    integration:
      name: 集成风险
      description: 外部依赖和集成点相关推理
      priority: medium
      subscribed: true
      artifacts: both
      guess: true
    interface-decision:
      name: 接口决策
      description: API/模块接口、函数签名、数据协议的设计选择推理
      priority: medium
      subscribed: true
      artifacts: design
    implementation-strategy:
      name: 实现策略
      description: 具体实现路径、算法选择、技术选型的推理
      priority: medium
      subscribed: true
      artifacts: design
    tech-tradeoff:
      name: 技术权衡
      description: 不同技术方案的对比分析
      priority: high
      subscribed: true
      artifacts: design
      guess: true
    migration:
      name: 迁移策略
      description: 数据或系统迁移相关推理
      priority: low
      subscribed: true
      artifacts: design
  业务:
    biz-rule:
      name: 业务规则
      description: 涉及业务流程、领域逻辑的推理
      priority: high
      subscribed: true
      artifacts: both
      guess: true
    capability-tradeoff:
      name: 能力权衡
      description: 新增、修改、移除能力的取舍推理
      priority: high
      subscribed: true
      artifacts: proposal
      guess: true
    stakeholder-impact:
      name: 利益相关方影响
      description: 对用户、运维、下游系统的影响评估推理
      priority: medium
      subscribed: true
      artifacts: proposal
  通用:
    scope-decision:
      name: 范围决策
      description: 关于功能范围边界的推理
      priority: medium
      subscribed: true
      artifacts: proposal
    assumption-link:
      name: 假设关联
      description: 推理基于未确认假设而非已验证事实的段落
      priority: high
      subscribed: true
      artifacts: both
      guess: true
```

## Behavior guardrails

### Must do
- Always load the YAML before annotating
- Filter categories by `artifacts` field before scanning — proposal-only categories must not fire on design.md and vice versa
- Only annotate reasoning that is actually present in the artifact
- Respect `subscribed` field — inactive categories must not produce annotations
- For `guess: true` categories, always include the `Basis` field (Fact / Assumption / Mixed)
- Cross-reference guess-governance Assumptions table (A-IDs) when present
- Always append the `## Reasoning Annotations` section to the artifact file
- Report which YAML file was used as the classification source
- Output a brief in-session notification including fact/assumption split

### Must not do
- Do NOT block or delay the OpenSpec propose workflow
- Do NOT modify existing artifact content — only append the annotation section at the end
- Do NOT annotate during code implementation (apply phase), on spec.md, or on tasks.md
- Do NOT invent reasoning that is not present in the artifact
- Do NOT apply proposal-only categories to design.md or design-only categories to proposal.md
- Do NOT dump the full annotation list into the session — the artifact is the source of truth
- Do NOT replace or override guess-governance — this skill is complementary

### Relationship with guess-governance
- guess-governance triages assumptions (A-01, A-02…) with L-level and Risk scoring
- reasoning-annotate labels reasoning and tags each passage's fact/assumption basis
- When both are active, reasoning-annotate cross-references the Assumptions table by A-ID
- guess-governance may block; reasoning-annotate never blocks
- The `assumption-link` category specifically catches reasoning that depends on unvalidated assumptions

## YAML maintenance

### User-initiated updates
The user may edit the YAML file directly at any time. The skill reads the latest version on each activation.

### AI-suggested updates
When the skill detects reasoning that does not fit any subscribed category but seems valuable:

> "⚠️ Unmatched high-value reasoning detected in `<section>` that does not fit any subscribed category. Consider adding a new category or enabling an inactive one. Run `/reasoning-annotate suggest` to see recommendations."

If the user runs `/reasoning-annotate suggest`:

1. Collect all unmatched passages from recent annotation passes
2. Analyze patterns — do they cluster around a theme?
3. Determine which `artifacts` scope the suggested category should have
4. If a clear pattern emerges, propose a new category entry to append under the appropriate domain
5. Present the suggestion for user approval before any file modification
6. If approved, append the entry to the project-level YAML (create the file if needed)

### Never auto-modify the YAML without explicit user approval.

## Annotation quality metrics

Track internally for calibration (output in the Guess awareness section when available):

| Metric | Description |
|--------|-------------|
| Detection count | Total reasoning passages identified |
| Coverage rate | Matched passages ÷ total detected |
| Category spread | How many distinct categories were matched |
| Unmatched rate | Unmatched passages ÷ total detected |
| Fact ratio | Fact-based annotations ÷ total annotations |
| Assumption ratio | Assumption-based annotations ÷ total annotations |
| Hot categories | Top 3 most frequently matched categories |

These metrics help the user calibrate their subscription — if coverage is low, they may need more categories; if a category never matches, it may be too narrow.
