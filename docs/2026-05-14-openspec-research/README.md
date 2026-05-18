# Epic 需求 QA 分析功能 - 调研总结

## 📋 调研概览

本次调研分析了 GitHub 和 Skill 生态中用于分析 Epic 需求 QA 的现有功能，重点关注需求落地过程中的需求管理、简易设计和提案草稿三个核心环节。

**调研日期**: 2026-05-14
**调研范围**: CodeRef Vault skill-vault 内已有内容
**主要发现**: OpenSpec 和 Impeccable 提供了良好的基础，但缺少专门的 Epic 级别 QA 管理功能

## 🔍 主要发现

### 现有工具分析

#### 1. OpenSpec - 需求规范管理框架
- ✅ **完整的变更生命周期管理**: propose → apply → sync → archive
- ✅ **需求探索模式**: `/opsx:explore` 支持开放式问答
- ✅ **结构化文档格式**: proposal, specs, design, tasks
- ⚠️ **缺少 Epic 级别视图**: 主要关注单一变更
- ❌ **QA 不持久化**: 探索模式不保存问答记录

#### 2. Impeccable - 前端设计计划模板
- ✅ **优秀的计划文档结构**: 问题框架、需求追溯、风险评估
- ✅ **系统化的决策记录**: 开放问题和决策历史
- ✅ **详细的实现分解**: Implementation Units
- ⚠️ **特定于前端设计**: 不通用于所有项目类型

### 功能空白分析

| 功能维度 | 现有支持 | 缺失部分 |
|---------|----------|----------|
| Epic 级别管理 | ⚠️ 部分 | ❌ 专门的 Epic 管理系统 |
| QA 问答记录 | ⚠️ 临时性 | ❌ 持久化结构化 QA |
| 需求追溯 | ✅ 基础支持 | ⚠️ 完整追溯链 |
| 文件命名规范 | ⚠️ 不统一 | ❌ 统一标准 |
| 进度追踪 | ⚠️ 任务级别 | ❌ Epic 级别进度 |

## 💡 推荐解决方案

### 核心功能扩展

基于 OpenSpec 框架，添加轻量级 Epic 管理功能：

```bash
# Epic 管理命令
/opsx:epic new <epic-name>              # 创建新 Epic
/opsx:epic show <epic-id>               # 显示 Epic 详情
/opsx:epic list                         # 列出所有 Epic
/opsx:epic archive <epic-id>            # 归档 Epic

# QA 管理命令
/opsx:qa ask <epic-id>                  # 提出新问题
/opsx:qa answer <question-id>           # 回答问题
/opsx:qa list <epic-id>                 # 列出所有问题
/opsx:qa search <query>                 # 搜索问题

# 追溯查询命令
/opsx:trace epic <epic-id>              # 查看 Epic 追溯链
/opsx:trace requirement <req-id>        # 查看需求追溯
/opsx:trace validate <epic-id>          # 验证追溯完整性
```

### 目录结构设计

```text
openspec/
├── epics/                           # 新增：Epic 管理目录
│   ├── EPIC-20260514-001-user-auth/
│   │   ├── epic.md                  # Epic 概览
│   │   ├── qa.md                    # QA 问答记录
│   │   ├── requirements/            # 需求详情
│   │   ├── design/                  # 设计文档
│   │   ├── changes/                 # 关联变更
│   │   ├── timeline.md              # 时间线规划
│   │   └── metadata.yaml            # Epic 元数据
│   └── .epic-index.yaml             # Epic 索引
├── changes/                         # 现有：具体变更管理
└── specs/                           # 现有：规范文档
```

## 📁 文件命名规范

### Epic 命名格式
```text
EPIC-{YYYYMMDD}-{id}-{short-name}
```
**示例**: `EPIC-20260514-001-user-auth`

### 需求文件命名
```text
REQ-{id}-{description}.md
```
**示例**: `REQ-001-user-registration.md`

### 设计文件命名
```text
DES-{id}-{component}.md
```
**示例**: `DES-001-auth-ui.md`

### 变更目录命名
```text
{verb}-{noun}
```
**示例**: `add-login-form`, `fix-auth-bug`

## 📊 追溯关系设计

### 完整追溯链
```text
Epic → Requirements → Design → Changes → Tasks
```

### 追溯查询示例
```bash
# 查看 Epic 完整追溯
/opsx:trace epic EPIC-20260514-001

# 输出示例:
EPIC: User Authentication
├── Requirements: REQ-001, REQ-002, REQ-003
├── Design: DES-001, DES-002
├── Changes: add-login-form, add-registration-form
└── Tasks: 12 tasks across 2 changes
```

## 🚀 实施建议

### 渐进式实施路径

#### Phase 1: 核心 Epic 管理 (2-3周)
- 实现 Epic 基本结构和模板
- 开发 EpicManager 核心功能
- 添加基础命令支持

#### Phase 2: QA 功能 (2-3周)
- 设计 QA 记录格式
- 开发 QAManager 功能
- 实现问题搜索和匹配

#### Phase 3: 追溯系统 (2-3周)
- 构建追溯关系图
- 开发 TraceManager 功能
- 添加完整性验证

#### Phase 4: 报告和优化 (1-2周)
- 实现报告生成
- 性能优化
- 文档完善

### 技术架构要点

```typescript
// 核心组件设计
class EpicManager {
  createEpic(name, description): Epic
  linkChange(epicId, changeId): void
  getTrace(epicId): EpicTrace
}

class QAManager {
  addQuestion(epicId, question): Question
  answerQuestion(questionId, answer): void
  findSimilarQuestions(question): Question[]
}

class TraceManager {
  buildTraceGraph(epicId): TraceGraph
  validateTrace(epicId): ValidationResult
  findEpicPath(from, to): TracePath[]
}
```

## 📈 预期价值

### 用户价值
- **更好的需求管理**: 清晰的 Epic 视图
- **提高沟通效率**: 结构化 QA 记录
- **增强可追溯性**: 完整的追溯链
- **改善协作体验**: 标准化文件格式

### 技术价值
- **轻量级扩展**: 最小侵入现有架构
- **向后兼容**: 不影响现有工作流
- **可扩展性**: 为未来功能提供基础

## 📚 相关文档

### 调研文档
1. **[调研总览](00-调研总览.md)** - 调研背景和主要发现
2. **[需求分析](01-需求分析.md)** - 用户需求和功能空白分析
3. **[简易设计](02-简易设计.md)** - 系统架构和技术设计
4. **[提案草稿](03-提案草稿.md)** - 实施计划和风险评估
5. **[文件命名规范](04-文件分类和命名规范.md)** - 命名标准和最佳实践

### 参考资源
- **OpenSpec 文档**: `/OpenSpec/docs/`
- **Impeccable 计划模板**: `/repos/impeccable/notes/plans/`
- **需求解析器**: `/OpenSpec/test/core/parsers/requirement-blocks.test.ts`

## 🎯 下一步行动

1. **审查提案**: 评估提案的可行性和优先级
2. **收集反馈**: 从潜在用户收集需求和反馈
3. **原型开发**: 开发核心功能原型
4. **逐步实施**: 按照渐进式路径实施功能

## 🔧 使用示例

### 创建 Epic 并管理 QA

```bash
# 1. 创建新 Epic
/opsx:epic new user-authentication
→ 创建 EPIC-20260514-001-user-authentication

# 2. 提出问题
/opsx:qa ask EPIC-20260514-001
→ 交互式问答，记录到 qa.md

# 3. 回答问题
/opsx:qa answer Q001
→ 提供答案，更新问题状态

# 4. 查看追溯
/opsx:trace epic EPIC-20260514-001
→ 显示完整的追溯关系

# 5. 生成报告
/opsx:report epic EPIC-20260514-001
→ 生成 Epic 状态报告
```

---

**调研人员**: Claude AI Agent
**文档版本**: 1.0
**最后更新**: 2026-05-14

**总结**: 基于 OpenSpec 和 Impeccable 的优秀基础，通过添加轻量级的 Epic 管理和 QA 功能，可以显著提升大型需求的管理和协作效率。建议采用渐进式实施策略，保持向后兼容的同时，为项目提供强大的 Epic 级别需求管理能力。