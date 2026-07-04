# Trae IDE 使用指南

> 完整的使用说明：如何把 evidence-first 规则装到 Trae IDE，怎么验证，怎么用。
> Trae-specific usage guide for the evidence-first rule.

## 1. 概述

`integrations/trae/SKILL.md` 是一个 **Trae Skill**（带 YAML frontmatter 的 Markdown 规则文件），被 Trae IDE 加载后会在特定关键词触发时强制 Agent：

1. **先调用工具**扫描代码（至少 3 个不同动作）
2. **再基于真实证据**给出分析（每个结论带 `[file:line]`）
3. **自我检查**清单通过后再输出

如果你之前用过 Cursor、Claude Code 等工具的规则文件，可以把 Trae Skill 类比成同样的东西——只是格式和触发机制是 Trae 特有的。

---

## 2. 安装

### 方式 1：全局安装（推荐 — 跨项目生效）

```bash
# 1. 创建 Trae 全局 Skill 目录
mkdir -p ~/.trae-cn/builtin/global/skills/evidence-first-analysis/

# 2. 下载规则文件
curl -o ~/.trae-cn/builtin/global/skills/evidence-first-analysis/SKILL.md \
  https://raw.githubusercontent.com/ztluck78/llm-agent-rules/main/integrations/trae/SKILL.md

# 3. 重启 Trae IDE
```

**优点**：装一次，所有项目都生效。
**适用**：长期使用、多个项目、希望 Trae 默认就遵守规则。

### 方式 2：项目级安装（仅当前项目生效）

```bash
cd /path/to/your/project

# 1. 在项目根目录创建 .trae/skills/
mkdir -p .trae/skills/evidence-first-analysis/

# 2. 下载规则文件
curl -o .trae/skills/evidence-first-analysis/SKILL.md \
  https://raw.githubusercontent.com/ztluck78/llm-agent-rules/main/integrations/trae/SKILL.md
```

**优点**：规则可提交到 git，团队成员克隆后自动生效。
**适用**：想先在一个项目试一下、或者团队要统一规则。

### 方式 3：临时测试（不进系统目录）

直接把 `SKILL.md` 的内容粘贴到 Trae 对话框的开头，让 Trae 当作"系统提示"使用。

**适用**：临时使用、不想动系统配置、想试试效果。

---

## 3. 验证（确认 Skill 已生效）

### 步骤 1：触发一次扫描

在 Trae 对话框输入：

```
全面分析这个项目的代码质量
```

### 步骤 2：检查响应是否合规

合规的响应**必须**同时满足以下 3 条：

| # | 检查项 | 合规示例 | 不合规示例 |
|---|--------|----------|------------|
| 1 | 开头是 `Scan complete.` | `Scan complete. Found 3 real issues...` | 直接开始讲"5 个原因" |
| 2 | 至少 3 个 `[file:line]` 引用 | `[src/index.ts:42]` | "通常 `index.ts` 有问题" |
| 3 | 修复建议是具体到文件行 | `Edit src/utils.ts:14-18: add null check` | "未来要注意 null 处理" |

### 步骤 3：如果不生效，排查以下 5 点

1. **文件路径是否正确**？
   - 全局：必须在 `~/.trae-cn/builtin/global/skills/evidence-first-analysis/SKILL.md`
   - 项目级：必须在 `<项目根>/.trae/skills/evidence-first-analysis/SKILL.md`

2. **frontmatter 完整吗**？
   文件**第一行**必须是 `---`，最后也是 `---`，中间是 YAML 配置。少了 `---` 不会被识别为 Skill。

3. **重启 Trae 了吗**？
   Skill 是启动时加载的；安装或修改后必须重启。

4. **关键词触发了吗**？
   你的提问必须包含触发关键词（见 §4）。否则 Skill 不会激活。

5. **Trae 版本支持 Skill 吗**？
   需要 Trae IDE 0.x 之后的版本。早于这个版本只能通过方式 3（粘贴到对话）。

---

## 4. 触发条件（什么情况下 Skill 激活）

Skill 在 Trae 检测到用户消息**包含**以下关键词时自动激活：

### 中文关键词

- `全面分析` / `系统排查` / `复盘` / `调研` / `全面了解`
- `为什么反复出这个问题` / `这个 bug 怎么产生的`

### 英文关键词

- `audit` / `investigation` / `review` / `assess` / `deep dive`

### 不会被触发的场景

- "帮我修一下这个 typo"（单一具体修改）
- "用 TypeScript 重写"（纯代码生成）
- "这段代码什么意思"（问答）
- 普通的 Tab 补全、单元测试编写（这些走 Trae 默认行为）

**经验法则**：你的问题越**开放性、越需要系统性理解**，Skill 越可能被激活。

---

## 5. 真实使用场景（5 个例子）

下面 5 个场景展示 Skill 激活后 Trae 应该怎么响应。

### 场景 1：跨文件 bug 调查

```
用户：这个用户登录失败的问题反复出现，全面排查一下
```

**期望 Trae 响应**：

```
Scan complete. Found 2 real issues (1 severe / 1 minor):

1. [src/auth/login.ts:42] 用户名比较用了 ===
   Evidence: grep 结果显示代码用了 `if (user.name === input.name)`
   但其他模块（如 src/auth/register.ts:18）已经改用 `localeCompare`。
   Severity: Medium

2. [src/auth/token.ts:15] Token 验证逻辑在异步上下文中未 await
   Evidence: 该函数被 src/middleware/auth.ts:8 调用，
   该调用点用了 await，但 token.ts 内部第 15 行的 jwt.verify 未 await。
   Severity: High

Systematic causes:
1. 国际化支持演进时未全量替换 — supported by finding #1
2. Promise 链重构时漏掉内层 await — supported by finding #2

Fix recommendations:
- Edit src/auth/login.ts:42 — 改用 `localeCompare` 保持一致
- Edit src/auth/token.ts:15 — 添加 await
- Add test at tests/auth/login.test.js: 同时覆盖中英文用户名
```

### 场景 2：性能问题复盘

```
用户：这个 API 越来越慢，复盘一下原因
```

**期望 Trae 响应**：

```
Scan complete. Found 1 real issue (1 severe):

1. [src/api/users.ts:23] 列表查询 N+1 问题
   Evidence: 该函数在循环里调用 `await db.profile.findOne({ userId })`，
   列表长度 50 时产生 51 次数据库查询。
   Test 输出（console.time）：平均 1.2s。
   Severity: High

Systematic cause:
- ORM 关系未用 include/inflate — supported by finding #1

Fix:
- Edit src/api/users.ts:23 — 改用 `db.user.findAll({ include: [Profile] })`
- Expected improvement: 1.2s → 80ms（基于 benchmark）
```

### 场景 3：风格违规审计

```
用户：审计代码看哪些地方违反了我们的 CSS 规范
```

**期望 Trae 响应**：

```
Scan complete. Found 3 real issues (0 severe / 3 minor):

1. [src/components/Button.module.css:12] 硬编码颜色 #7B33CC
   Evidence: 规范要求使用 var(--primary-light)，全文 6 处硬编码。
   Severity: Low

2. [src/components/Card.module.css:8] 缺少 min-width: 0
   Evidence: flex item 的 skill tag 在 375px 视口下溢出。
   E2E 测试复现：tests/e2e/card-skills-overflow.spec.js
   Severity: Medium

3. [src/pages/Home.module.css:45] 未使用语义化 token
   Evidence: 使用了 `var(--gray-100)` 但项目无该 token，应为 `var(--bg-section)`。
   Severity: Low
```

### 场景 4：新代码 review

```
用户：review 一下我刚写的 useUser hook
```

**期望 Trae 响应**：

```
Scan complete. Read src/hooks/useUser.js. Found 2 real issues:

1. [src/hooks/useUser.js:14] 缺少 cleanup
   Evidence: useEffect 内部订阅了 WebSocket，但返回的 cleanup 函数
   没有调用 socket.close()。组件卸载时内存泄漏。
   Severity: High

2. [src/hooks/useUser.js:23] race condition
   Evidence: setUser 在异步 fetch 回调中调用，但没检查组件是否已卸载。
   React 18 严格模式下会触发 "Can't perform state update on unmounted component" 警告。
   Severity: Medium
```

### 场景 5：跨模块架构理解

```
用户：调研一下项目的数据流是怎样的
```

**期望 Trae 响应**：

```
Scan complete. Inspected 8 files. Mapped the data flow:

1. [src/main.tsx:5] 入口创建 React 根
2. [src/App.tsx:12] 路由分发
3. [src/contexts/AuthContext.jsx:18] 全局认证状态
4. [src/hooks/useAuth.js:5] 暴露给组件
5. [src/api/auth.js:23] 调用后端

Issue found:
- [src/api/auth.js:42] Token refresh 失败时无降级策略
  Evidence: try/catch 仅 console.error，组件层无降级 UI。
  Severity: Medium
```

---

## 6. Trae 工具映射

Skill 期望 Trae Agent 使用以下工具收集证据：

| 任务 | Trae 工具 | 示例 |
|------|-----------|------|
| 读文件 | `Read` | `Read /path/to/file.ts` |
| 跑命令 | `RunCommand` | `RunCommand npm test` |
| 搜索文本 | `Grep` | `Grep pattern="TODO" path=src/` |
| 列文件 | `Glob` | `Glob pattern="src/**/*.ts"` |
| 看 Git 历史 | `RunCommand` | `RunCommand git log --oneline -10` |

**至少调用 3 个不同类别的工具**才能算合规扫描。

---

## 7. 故障排查

### 问题 1：响应还是泛泛的"5 个原因"

**原因**：Skill 没被加载，或者被其他 Skill 覆盖。
**解决**：
1. 检查路径（见 §3 步骤 3）
2. 重启 Trae
3. 在 Trae 设置里确认 Skill 已启用

### 问题 2：响应合规了，但太啰嗦

**原因**：Skill 不限制响应长度。
**解决**：
- 在提问时加上"用最少的字数" / "≤ 200 字"
- 长期方案：编辑 `SKILL.md` 的 Output Structure 章节，添加长度约束

### 问题 3：响应里 `[file:line]` 引用是错的

**原因**：Agent 引用了"看起来像"的行号，没真正读。
**解决**：
- 反馈给 Agent，让它重新读
- 长期：使用 [common-pitfalls.md §Pitfall 7](../../docs/common-pitfalls.md#pitfall-7-citing-lines-that-dont-exist) 提示 Agent 自查

### 问题 4：Skill 在某些项目不生效

**原因**：项目级 Skill 优先级可能被覆盖。
**解决**：
- 检查 `.trae/skills/` 目录是否在 git 仓库根（不是子目录）
- 确认 `.traeignore` 没排除该目录
- 改用全局安装（方式 1）

### 问题 5：想禁用 Skill（临时）

**解决**：
- 在 Trae 设置里临时关闭
- 或者在提问时显式说："**不要用 evidence-first 规则**，直接给答案"

---

## 8. 高级用法

### 自定义 Skill

复制 `SKILL.md` 到另一个目录（如 `.trae/skills/team-specific/`），修改内容。Trae 支持多 Skill 共存，按优先级生效。

**适用场景**：
- 团队有特殊规范（如必须用某个 lint 工具）
- 项目有特殊的扫描动作（如先跑 `npm run test:e2e`）

### 与其他工具的 Skill 组合

Trae 可以同时加载多个 Skill。常见组合：

| 组合 | 效果 |
|------|------|
| `evidence-first-analysis` + `code-review` | 扫描证据 + 给出 review 评论 |
| `evidence-first-analysis` + `security-audit` | 扫描证据 + 安全漏洞检查 |

每个 Skill 在自己的触发词下激活，互不干扰。

### 链接到项目的 docs

在项目根 `AGENTS.md` 加上：

```markdown
## Trae Skills

本项目启用了 evidence-first-analysis Skill。
安装：见 https://github.com/ztluck78/llm-agent-rules/blob/main/integrations/trae/README.md
```

新成员 onboarding 时一目了然。

---

## 9. 卸载

```bash
# 全局安装
rm -rf ~/.trae-cn/builtin/global/skills/evidence-first-analysis/

# 项目级安装
rm -rf .trae/skills/evidence-first-analysis/
```

然后重启 Trae。

---

## 10. 相关链接

- [核心规则](../../rules/evidence-first-analysis.md) — 规则的完整定义
- [失败案例](../../rules/failure-cases.md) — 没遵循规则时 Trae 会出现什么错误输出
- [常见误区](../../docs/common-pitfalls.md) — Skill 看起来生效但实际是"伪合规"的 10 个陷阱
- [设计原理](../../rules/design-rationale.md) — 为什么规则要这样设计
- [集成目录 README](./README.md) — 安装和快速验证步骤
- [主仓库 README](../../README.md) — 跨工具的总览

## License

MIT — see [LICENSE](../../LICENSE).
