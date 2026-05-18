## Context

### 问题现状

AI 辅助开发中的规划方法论呈现两极化格局：

**Cursor Plan Mode** — 即时性轻量规划：
- 内嵌于 IDE 的 Plan → Approve → Execute 二阶段模式
- 上下文由 IDE 隐式管理，零学习成本
- 适合函数级到模块级任务，无持久化制品
- 社区反馈可防止约 90% 的架构问题
- 局限：无角色系统、无质量门控、大型任务力不从心

**BMAD-METHOD v6** — 全周期结构化框架：
- Analysis → Planning → Solutioning → Implementation 四阶段生命周期
- 6 角色专业化 Agent 系统（Mary/BA、Paige/TW、John/PM、Sally/UX、Winston/SA、Amelia/SSE）
- 文件化渐进式上下文传递（PRD → Architecture → Story → Code）
- 完整制品体系、质量门控、可审计决策链
- 局限：学习曲线陡峭、对中小型任务过重

**空白地带**: 在"零规划"和"全周期规划"之间，没有一个方法论能够自适应地选择合适的规划深度。

### 设计输入来源

| 来源 | 借鉴要素 |
|------|----------|
| Cursor Plan | Plan-then-Execute 二阶段模式、IDE 隐式上下文、人类审批门控 |
| BMAD-METHOD | 四阶段生命周期、渐进式上下文传递、角色专业化、质量门控、制品模板 |

## Goals / Non-Goals

**Goals:**

- 定义四级规划深度模型（Level 0-3），每级有明确的适用条件、流程和制品要求
- 设计双轨上下文管理机制，支持隐式上下文和显式上下文的动态切换
- 设计弹性角色系统，根据规划级别渐进式激活 Agent 角色
- 确保各级别之间的平滑升降级机制
- 每个级别产出可直接被下游工具（如 OpenSpec）消费的制品

**Non-Goals:**

- 不设计具体的代码实现或执行引擎
- 不替代 OpenSpec 的 propose/apply/archive 工作流，而是为其提供上游输入
- 不设计 IDE 集成的具体技术方案
- 不涉及 CI/CD、部署、测试执行等实施细节
- 不重新定义 BMAD 的 Agent 人格或 Cursor 的编辑器功能

## Decisions

### Decision 1: 四级规划深度模型

采用四级模型，覆盖从零规划到全周期规划的完整光谱：

```
Level 0: 即时执行
  触发: 简单修改、格式化、文档更新
  流程: 直接执行，无规划步骤
  制品: 无
  角色: 无（单一 Agent）

Level 1: 快速规划（Cursor Plan 风格）
  触发: 函数级开发、Bug 修复、小功能添加
  流程: Research → Plan → Approve → Execute
  制品: 内嵌对话的 Markdown 计划（非持久化）
  角色: 无（单一 Agent）

Level 2: 结构化规划（轻量 BMAD）
  触发: 模块级功能、跨文件重构、API 设计
  流程: Analysis → Design → Review → Plan
  制品: 持久化的设计文档 + 任务清单
  角色: 轻量（规划者 + 实现者）

Level 3: 全周期规划（完整 BMAD）
  触发: 新产品、大型架构、多模块系统
  流程: Analysis → PRD → Architecture → Stories
  制品: 完整制品体系（PRD + Arch + Stories + Sprint Status）
  角色: 完整六角色
```

**理由**: 四级模型对应了开发者日常遇到的四种典型任务复杂度。Cursor Plan 覆盖 Level 0-1，BMAD 覆盖 Level 3，Level 2 是两者之间的空白地带。

### Decision 2: 复杂度评估维度

任务复杂度通过以下维度量化：

| 维度 | 权重 | Level 0 | Level 1 | Level 2 | Level 3 |
|------|------|---------|---------|---------|---------|
| 影响文件数 | 高 | ≤ 1 | ≤ 5 | ≤ 15 | > 15 |
| 跨模块程度 | 高 | 0 | 1 | 2-3 | > 3 |
| 架构影响 | 中 | 无 | 无 | API/DB 变更 | 架构重构 |
| 新依赖引入 | 低 | 无 | 无 | 少量 | 大量 |
| 不确定性 | 中 | 低 | 低 | 中 | 高 |

**理由**: 这五个维度覆盖了任务复杂度的主要影响因素。权重可根据项目类型调整。

### Decision 3: 双轨上下文管理

```
轨道1: 隐式上下文（Level 0-1）
  ├── IDE 代码库索引
  ├── 打开文件和光标位置
  ├── @引用和对话历史
  └── 生命周期: 会话级

轨道2: 显式上下文（Level 2-3）
  ├── 项目上下文文件 (project-context.md)
  ├── 决策日志 (.decision-log.md)
  ├── 阶段制品文件 (design.md, architecture.md, ...)
  └── 生命周期: 项目级

升降级转换:
  Level 1 → Level 2: 将隐式上下文关键信息显式化写入文件
  Level 3 → Level 1: 从制品中提取摘要作为对话上下文注入
```

**理由**: Cursor Plan 的隐式上下文在低级别任务中足够高效；BMAD 的显式上下文在高级别任务中保证信息不丢失。双轨设计避免了"一刀切"的上下文策略。

### Decision 4: 弹性角色系统

```
Level 0-1: 单一 Agent
  无角色区分，Agent 覆盖所有职能

Level 2: 双角色
  ├── 规划者: 合并 PM + Architect 职能
  └── 实现者: 合并 Dev + QA 职能

Level 3: 完整六角色
  ├── Mary (BA)     → 分析
  ├── John (PM)     → 规划
  ├── Sally (UX)    → 规划
  ├── Winston (Arch)→ 方案设计
  ├── Amelia (Dev)  → 实施
  └── Paige (TW)    → 跨阶段文档
```

**理由**: 角色专业化带来思维多样性和质量保证，但对简单任务是不必要的开销。弹性系统让角色激活与任务复杂度匹配。

### Decision 5: 轻量门控机制

```
Level 0: 无门控
Level 1: 人类审批（Approve/Reject/Modify）
Level 2: 人类审批 + 关键问题检查清单
Level 3: 质量评分矩阵 + 就绪检查 + 人类审批
```

**理由**: 门控强度与规划深度正相关。Cursor Plan 的单点审批在低级别足够；BMAD 的多重门控在高级别必要。

### Decision 6: 制品与 OpenSpec 对接

各级别的制品设计为可直接作为 OpenSpec propose 流程的输入：

| 级别 | 本方法制品 | OpenSpec 对接点 |
|------|-----------|----------------|
| Level 0 | 无 | N/A |
| Level 1 | 对话内嵌计划 | 用户可手动提取为 proposal.md |
| Level 2 | design.md + tasks.md | 直接映射到 OpenSpec 的 design.md + tasks.md |
| Level 3 | PRD + Architecture + Stories | 通过 OpenSpec 的 `/opsx:propose` 转换为 proposal.md + specs/ |

**理由**: 与 OpenSpec 的对接确保了从规划到执行的平滑过渡。规划阶段的产出直接成为执行阶段的输入。

## Risks / Trade-offs

### 级别判定不准确

**风险**: 复杂度评估可能误判任务级别，导致过度规划或规划不足。

**缓解**:
- 允许用户手动覆盖自动评估结果
- 提供"先低后升"策略——从低级别开始，遇到复杂度提升时自动升级
- 积累历史数据优化评估算法

### 双轨上下文的一致性

**风险**: 隐式上下文和显式上下文之间可能出现信息不一致。

**缓解**:
- Level 升级时执行显式化检查清单
- 显式上下文文件标记同步时间戳
- 设计上下文校验规则

### 方法论学习成本

**风险**: 虽然比完整 BMAD 轻量，但四级模型仍然增加了认知负担。

**缓解**:
- Level 0-1 的使用成本接近零（与当前 Cursor Plan 体验一致）
- 自动评估降低用户决策负担
- 提供简洁的级别说明和示例

### 与现有工具的兼容性

**风险**: 双轨上下文和弹性角色可能不被现有 AI 编码工具原生支持。

**缓解**:
- Level 0-1 完全兼容现有工具
- Level 2-3 通过 skill/插件机制扩展，不修改工具本身
- 制品格式基于标准 Markdown，通用性高

## Open Questions

1. **级别阈值如何校准**: 五个维度的权重和阈值需要通过实际项目数据校准。初始值基于经验设定，是否合理？
2. **Level 1→2 的转换时机**: 在执行过程中发现任务比预期复杂时，何时应该暂停并升级到 Level 2？
3. **多角色系统的实现方式**: Level 3 的六角色是通过 subagent 实现，还是通过单一 Agent 的 prompt switching 实现？对 LLM 上下文窗口的要求是什么？
4. **制品格式标准化**: Level 2 的 design.md 和 tasks.md 是否需要严格的 schema 约束，还是保持灵活的 Markdown 格式？
5. **与 OpenSpec 的映射规则**: Level 3 的 PRD + Architecture 如何精确映射到 OpenSpec 的 proposal.md + specs/ 格式？是否需要转换层？
6. **团队协作场景**: 当多人同时在不同级别工作时，如何协调上下文和制品的同步？
