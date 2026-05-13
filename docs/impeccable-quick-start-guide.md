# Impeccable 快速上手指南

## 安装和配置

### 1. 安装方法

#### 方法一：从官网下载（推荐）
访问 https://impeccable.style 下载对应工具的 ZIP 包并解压到项目目录。

#### 方法二：从仓库复制

**Claude Code:**
```bash
# 项目级安装
cp -r dist/claude-code/.claude your-project/

# 全局安装（应用到所有项目）
cp -r dist/claude-code/.claude/* ~/.claude/
```

**Cursor:**
```bash
cp -r dist/cursor/.cursor your-project/
```

**其他工具:**
```bash
# Gemini CLI
cp -r dist/gemini/.gemini your-project/

# OpenCode
cp -r dist/opencode/.opencode your-project/

# GitHub Copilot
cp -r dist/github/.github your-project/
```

### 2. 验证安装

```bash
# 检查技能是否可用
/impeccable

# 应该显示 23 个命令的菜单
```

## 核心工作流程

### 步骤 1: 建立设计上下文

```bash
/impeccable teach
```

这将引导你创建项目级的 `PRODUCT.md` 和 `DESIGN.md` 文件，定义：
- 目标用户
- 产品目的
- 品牌个性
- 设计原则
- 配色方案
- 字体系统
- 组件规范

### 步骤 2: 选择正确的寄存器

Impeccable 支持两种设计寄存器：

**Brand（品牌）**: 设计即产品
- 营销页面
- 落地页
- 品牌网站
- 活动页面

**Product（产品）**: 设计服务产品
- 应用 UI
- 管理后台
- 仪表板
- 工具界面

UrbanManagement 系统主要使用 **Product 寄存器**。

### 步骤 3: 使用专业命令

## 常用命令详解

### 1. 审计命令 (audit)

**用途**: 运行技术质量检查，生成详细报告

```bash
# 审计整个项目
/impeccable audit

# 审计特定模块
/impeccable audit dashboard/
/impeccable audit tables/
/impeccable audit forms/
```

**输出**: 
- 可访问性评分 (0-4)
- 性能评分 (0-4)
- 响应式设计评分 (0-4)
- 主题化评分 (0-4)
- 反模式检测评分 (0-4)

### 2. 提炼命令 (extract)

**用途**: 将可重用的组件和设计令牌提取到设计系统中

```bash
# 提取整个组件库
/impeccable extract components/

# 提取特定类型的组件
/impeccable extract buttons/
/impeccable extract forms/
/impeccable extract tables/
```

### 3. 文档化命令 (document)

**用途**: 从现有代码生成 DESIGN.md 文档

```bash
# 生成完整的设计文档
/impeccable document
```

### 4. 优化命令

**性能优化**:
```bash
/impeccable optimize dashboard/
```

**适配不同设备**:
```bash
/impeccable adapt mobile-views
/impeccable adapt tablet-views
```

**强化生产准备**:
```bash
/impeccable harden checkout/
/impeccable harden forms/
```

### 5. 设计命令

**添加动画**:
```bash
/impeccable animate interactions/
```

**改善色彩**:
```bash
/impeccable colorize charts/
/impeccable colorize dashboard/
```

**优化排版**:
```bash
/impeccable typeset content/
/impeccable typeset tables/
```

**改善布局**:
```bash
/impeccable layout dashboard/
/impeccable layout forms/
```

### 6. 修饰命令 (polish)

**用途**: 发布前的最终质量检查

```bash
/impeccable polish entire-system
/impeccable polish dashboard/
/impeccable polish forms/
```

## CLI 工具使用

### 反模式检测

```bash
# 检测目录
npx impeccable detect src/

# 检测文件
npx impeccable detect index.html

# 检测 URL
npx impeccable detect https://example.com

# 快速模式，JSON 输出
npx impeccable detect --fast --json src/
```

### 浏览器实时模式

```bash
# 启动浏览器覆盖服务器
npx impeccable live
```

## 实际使用示例

### 场景 1: 新项目启动

```bash
# 1. 建立设计上下文
/impeccable teach

# 2. 规划核心界面
/impeccable shape dashboard

# 3. 构建功能
/impeccable craft user-profile

# 4. 提取可复用组件
/impeccable extract components/

# 5. 生成设计文档
/impeccable document
```

### 场景 2: 现有项目优化

```bash
# 1. 审计现有界面
/impeccable audit

# 2. 根据审计结果进行针对性优化
/impeccable adapt mobile-views
/impeccable optimize performance
/impeccable harden error-handling

# 3. 最终修饰
/impeccable polish
```

### 场景 3: 数据可视化优化

```bash
# 1. 审计图表组件
/impeccable audit charts/

# 2. 优化色彩策略
/impeccable colorize charts/

# 3. 改善排版
/impeccable typeset charts/

# 4. 优化布局
/impeccable layout dashboard/
```

### 场景 4: 可访问性改进

```bash
# 1. 运行可访问性审计
/impeccable audit

# 2. 查看可访问性评分和建议
# 3. 根据建议进行修复

# 4. 验证改进
/impeccable audit
```

## 高级功能

### 命令固定

创建独立快捷方式：

```bash
# 将 audit 固定为 /audit
/impeccable pin audit

# 现在可以直接使用
/audit dashboard/

# 取消固定
/impeccable unpin audit
```

### 批量操作

```bash
# 对多个模块执行相同操作
/impeccable audit dashboard/ tables/ forms/

# 依次执行多个命令
/impeccable audit && /impeccable adapt && /impeccable polish
```

## 最佳实践

### 1. 渐进式采用

- 从非关键模块开始试点
- 逐步扩展到整个系统
- 持续迭代改进

### 2. 团队协作

- 建立统一的设计上下文
- 共享设计系统和组件库
- 定期进行设计审查

### 3. 度量和改进

- 跟踪审计评分的变化
- 记录设计决策和理由
- 建立设计质量指标

### 4. 持续集成

```bash
# 在 CI/CD 中集成
npx impeccable detect src/ --fast --json
```

## 常见问题

### Q: 如何选择正确的寄存器？

**A**: 考虑以下因素：
1. 任务本身的关键词（"landing page" → Brand，"dashboard" → Product）
2. 当前工作的页面/文件/路由
3. PRODUCT.md 中的 `register` 字段

### Q: 审计评分如何解读？

**A**: 
- **18-20**: 优秀（小的修饰）
- **14-17**: 良好（需要改进弱项）
- **10-13**: 可接受（需要大量工作）
- **6-9**: 差（需要重大改造）
- **0-5**: 关键（基础性问题）

### Q: 如何处理 AI 模式检测结果？

**A**: 
1. 不要忽视任何检测到的模式
2. 使用 `/impeccable polish` 移除常见模式
3. 根据具体情况使用专门的修复命令

### Q: 多久应该运行一次审计？

**A**: 
- 开发期间：每周一次
- 发布前：必须运行
- 重大更新后：立即运行

## 下一步

1. **立即开始**: 在你的项目中运行 `/impeccable teach`
2. **深入学习**: 阅读 `repos/impeccable/skill/reference/` 中的详细参考文档
3. **实践应用**: 在实际项目中使用各个命令
4. **持续改进**: 定期审计和优化

---

**需要帮助？**

- 查看详细文档: `repos/impeccable/README.md`
- 查看命令参考: `repos/impeccable/skill/reference/`
- 访问官网: https://impeccable.style