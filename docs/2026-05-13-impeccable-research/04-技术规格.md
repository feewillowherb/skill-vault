# Impeccable 技术规格说明

## 系统架构

### 核心组件

Impeccable v3.0+ 采用统一的技能架构：

- **一个用户可调用技能**: `impeccable`
- **23 个子命令**: 通过路由表分发
- **7 个设计领域参考**: 加载到每个命令中
- **27 个反模式检测规则**: 确定性规则 + LLM 审查

### 目录结构

```
impeccable/
├── skill/                    # 技能定义源文件
│   ├── SKILL.md             # 主技能定义（路由表、设计法则）
│   ├── reference/           # 命令和领域参考
│   │   ├── craft.md
│   │   ├── audit.md
│   │   ├── typography.md
│   │   ├── color-and-contrast.md
│   │   └── ...
│   └── scripts/             # 构建和工具脚本
├── cli/                     # 独立 CLI 工具
│   ├── bin/                # CLI 入口点
│   ├── engine/             # 反模式检测引擎
│   └── lib/                # CLI 共享库
├── dist/                    # 构建输出（各平台格式）
│   ├── claude-code/
│   ├── cursor/
│   ├── gemini/
│   └── ...
└── site/                    # 官网和文档
```

## 技术规格

### 支持的平台

| 平台 | 技能目录 | 配置文件 | 状态 |
|------|----------|----------|------|
| Claude Code | `.claude/skills/` | `CLAUDE.md` | ✅ 完全支持 |
| Cursor | `.cursor/skills/` | `.cursorrules` | ✅ 完全支持 |
| Gemini CLI | `.gemini/skills/` | 配置文件 | ✅ 完全支持 |
| Codex CLI | `.agents/skills/` | `agents/openai.yaml` | ✅ 完全支持 |
| VS Code Copilot | `.github/skills/` | `.github/skills/` | ✅ 完全支持 |
| Kiro | `.kiro/skills/` | 配置文件 | ✅ 完全支持 |
| OpenCode | `.opencode/skills/` | 配置文件 | ✅ 完全支持 |
| Pi | `.pi/skills/` | 配置文件 | ✅ 完全支持 |
| Qoder | `.qoder/skills/` | 配置文件 | ✅ 完全支持 |
| Trae | `.trae/skills/` | 配置文件 | ✅ 完全支持 |
| Rovo Dev | `.rovodev/skills/` | 配置文件 | ✅ 完全支持 |

### 前端元数据（Frontmatter）

Impeccable 技能使用标准化的前端元数据：

```yaml
---
name: impeccable
description: "Use when the user wants to design, redesign, shape, critique, audit..."
argument-hint: "[{{command_hint}}] [target]"
user-invocable: true
allowed-tools:
  - Bash(npx impeccable *)
license: Apache 2.0
---
```

### 变量替换系统

构建时替换的占位符：

| 占位符 | 描述 | 示例值 |
|--------|------|--------|
| `{{model}}` | 模型名称 | Claude, Gemini, GPT |
| `{{config_file}}` | 配置文件名 | CLAUDE.md, .cursorrules |
| `{{ask_instruction}}` | 如何询问用户 | 不同平台语法 |
| `{{command_prefix}}` | 命令前缀 | `/` 或 `$` |
| `{{available_commands}}` | 可用命令列表 | 自动填充 |
| `{{scripts_path}}` | 脚本目录路径 | 平台感知路径 |

## 反模式检测引擎

### 规则分类

#### AI 模式检测（Slop）
```javascript
{
  id: 'side-tab',
  category: 'slop',
  name: 'Side-stripe borders',
  description: 'Colored border-left or border-right on cards/list items'
}
```

#### 设计质量检测（Quality）
```javascript
{
  id: 'low-contrast',
  category: 'quality',
  name: 'Low contrast text',
  description: 'Text contrast ratio below 4.5:1'
}
```

### 检测方法

1. **纯函数检查** (`checkXxx(opts)`): 无 DOM 访问
2. **浏览器适配器** (`checkElementXxxDOM(el)`): 使用 `getComputedStyle`
3. **jsdom 适配器** (`checkElementXxx(el, tag, window)`): 解析 CSS

### CLI 集成

```bash
# 基础检测
npx impeccable detect src/

# URL 检测（需要 Puppeteer）
npx impeccable detect https://example.com

# 快速模式，仅正则表达式
npx impeccable detect --fast --json src/
```

## 设计上下文系统

### PRODUCT.md 结构

```markdown
## Register
product

## Users
Frontend and full-stack developers who use AI coding tools...

## Product Purpose
Impeccable gives developers a shared design vocabulary...

## Brand Personality
Expert, opinionated, refined...

## Design Principles
1. Practice what you preach
2. Show, don't tell
3. Expert confidence
```

### DESIGN.md 结构

```markdown
---
name: Impeccable
description: Warm-paper editorial sanctuary...

colors:
  editorial-magenta: "oklch(60% 0.25 350)"
  warm-ash-cream: "oklch(96% 0.005 350)"
  ...

typography:
  display:
    fontFamily: "Cormorant Garamond, Georgia, serif"
    fontSize: "clamp(2.5rem, 7vw, 4.5rem)"
    ...

components:
  button-primary:
    backgroundColor: "{colors.deep-graphite}"
    ...
---
```

### 上下文加载器

```bash
node .claude/skills/impeccable/scripts/load-context.mjs
```

**输出**: JSON 格式的完整上下文
**查找顺序**: 
1. 项目根目录
2. `.agents/context/`
3. `docs/`

## 命令路由系统

### 路由规则

1. **无参数**: 显示命令菜单
2. **第一个词匹配命令**: 加载对应的参考文件
3. **第一个词不匹配**: 通用设计调用

### 命令元数据

```json
{
  "craft": {
    "description": "Shape, then build a feature end-to-end",
    "argumentHint": "[feature]",
    "category": "Build"
  },
  "audit": {
    "description": "Technical quality checks",
    "argumentHint": "[target]",
    "category": "Evaluate"
  }
}
```

## 构建系统

### 构建命令

```bash
# 构建所有平台
bun run build

# 清理并重新构建
bun run rebuild

# 构建浏览器版本
bun run build:browser

# 构建扩展版本
bun run build:extension
```

### 版本管理

三个独立的版本组件：

1. **CLI**: `package.json` → `version`
2. **Skills**: `.claude-plugin/plugin.json` → `version`
3. **Extension**: `extension/manifest.json` → `version`

### 发布流程

```bash
# 1. 版本 bump
# 2. 添加变更日志到 site/pages/index.astro
# 3. 提交并推送
# 4. 运行发布脚本
bun run release:<skill|cli|ext>
```

## 测试框架

### 单元测试

```bash
# 运行默认测试套件
bun run test

# 运行特定测试
bun test tests/build.test.js
```

### 固定测试（Fixture Tests）

```bash
# 使用 Node 运行（Bun 对 jsdom 支持不好）
node --test tests/detect-antipatterns-fixtures.test.mjs
```

### E2E 测试

```bash
# 运行完整 E2E 测试（需要 Playwright）
bun run test:live-e2e

# 运行特定固定测试
IMPECCABLE_E2E_ONLY=vite8-react-modal bun run test:live-e2e

# 使用 LLM 代理（需要 API Key）
IMPECCABLE_E2E_AGENT=llm bun run test:live-e2e
```

## 浏览器实时模式

### 架构

- **服务器**: Node.js 服务器提供覆盖界面
- **客户端**: 浏览器扩展注入脚本
- **代理**: 假代理生成变体（或 LLM 代理）

### 启动

```bash
npx impeccable live
```

### 代理接口

```javascript
// 生成变体的接口
generateVariants(event, context) {
  return {
    scopedCss: "...",
    variants: [
      {
        range: { min: 0, max: 100, value: 50 },
        steps: { values: [0, 50, 100] },
        toggle: { enabled: true }
      }
    ]
  }
}
```

## 设计令牌系统

### OKLCH 色彩空间

Impeccable 使用 OKLCH 而非 HSL：

```css
/* 传统 HSL */
color: hsl(350, 80%, 60%);

/* OKLCH（Impeccable 推荐） */
color: oklch(60% 0.25 350);
```

**优势**:
- 感知均匀性
- 更好的色彩插值
- 广色域支持

### 色彩策略层级

1. **Restrained**: 着色中性色 + 单一强调色 ≤10%
2. **Committed**: 单一饱和色占据 30-60%
3. **Full palette**: 3-4 个命名角色
4. **Drenched**: 表面即色彩

## 性能优化

### 检测的性能问题

1. **布局抖动**: 在循环中读/写布局属性
2. **昂贵动画**: 布局属性动画、无边界模糊
3. **缺少优化**: 无懒加载、未优化资源
4. **束包大小**: 不必要的导入、未使用依赖

### 优化建议

```javascript
// ❌ 错误：布局抖动
elements.forEach(el => {
  el.style.height = el.offsetHeight + 10 + 'px';
});

// ✅ 正确：批量读写
const heights = elements.map(el => el.offsetHeight);
elements.forEach((el, i) => {
  el.style.height = heights[i] + 10 + 'px';
});
```

## 可访问性标准

### WCAG 2.1 AA 合规性

- **对比度**: 文本 4.5:1，大文本 3:1
- **键盘导航**: 所有交互可键盘访问
- **语义 HTML**: 正确的标题层次、地标
- **ARIA**: 适当的角色、标签、状态
- **表单**: 输入标签、错误消息、必填指示器

### 检测项目

```javascript
// 对比度检查
const contrastRatio = getContrast(foreground, background);
if (contrastRatio < 4.5) {
  report('low-contrast', { ratio: contrastRatio });
}

// 键盘导航检查
if (!hasFocusIndicator(element)) {
  report('missing-focus-indicator');
}
```

## 响应式设计

### 断点标准

```css
/* 移动优先 */
@media (min-width: 640px)  { /* 小屏幕 */ }
@media (min-width: 768px)  { /* 平板 */ }
@media (min-width: 1024px) { /* 桌面 */ }
@media (min-width: 1280px) { /* 大屏幕 */ }
```

### 触摸目标

- **最小尺寸**: 44×44px
- **间距**: 目标之间至少 8px
- **避免**: 小于 32×32px 的交互元素

### 检测方法

```javascript
// 触摸目标检查
const touchTarget = element.getBoundingClientRect();
if (touchTarget.width < 44 || touchTarget.height < 44) {
  report('small-touch-target', { size: touchTarget });
}
```

## 扩展和定制

### 添加新命令

1. 创建 `skill/reference/<command>.md`
2. 更新 `skill/SKILL.md` 路由表
3. 添加到 `scripts/lib/utils.js` 的 `IMPECCABLE_SUB_COMMANDS`
4. 更新 `skill/scripts/command-metadata.json`
5. 运行 `bun run build`

### 添加反模式规则

1. 创建固定测试 `tests/fixtures/antipatterns/{rule-id}.html`
2. 添加失败测试到 `tests/detect-antipatterns-fixtures.test.mjs`
3. 实现纯检查函数 `checkXxx(opts)`
4. 添加浏览器和 jsdom 适配器
5. 更新 `cli/engine/detect-antipatterns.mjs`
6. 运行 `bun run build:browser && bun run build:extension && bun run test`

## 安全和隐私

### 数据处理

- **本地处理**: 所有检测在本地运行
- **无外部请求**: 不向外部服务发送数据
- **可选 LLM**: 只有显式启用时使用 API

### 权限模型

```yaml
allowed-tools:
  - Bash(npx impeccable *)
```

**限制**: 只允许运行 `impeccable` 相关命令

## 维护和支持

### 版本策略

- **主版本**: 重大架构变更
- **次版本**: 新功能或命令
- **补丁版本**: Bug 修复和小改进

### 更新频率

- **CLI**: 按需发布
- **Skills**: 定期更新
- **Extension**: 独立发布

### 社区资源

- **GitHub**: https://github.com/pbakaus/impeccable
- **NPM**: https://www.npmjs.com/package/impeccable
- **官网**: https://impeccable.style
- **讨论**: GitHub Discussions

---

**技术支持**: 
- 查看 `repos/impeccable/DEVELOP.md` 了解贡献指南
- 查看 `repos/impeccable/AGENTS.md` 了解代理使用
- 查看 `repos/impeccable/CLAUDE.md` 了解架构细节