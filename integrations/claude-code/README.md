# Claude Code Integration

> Evidence-first rule packaged for Claude Code's `CLAUDE.md` format.
> 把 evidence-first 规则接入 Claude Code 终端助手。

## 文件清单 / Files in this directory

| 文件 | 用途 |
|------|------|
| `CLAUDE.md` | Claude Code 项目级规则文件 |

## 安装 / Installation

### 项目级（推荐 — 仅当前项目生效）

```bash
# 在项目根目录创建 CLAUDE.md
cp CLAUDE.md /path/to/your/project/CLAUDE.md
```

### 用户级（所有项目生效）

```bash
# Claude Code 用户级规则
mkdir -p ~/.claude/
cp CLAUDE.md ~/.claude/CLAUDE.md
```

### 仓库级（团队共享）

把 `CLAUDE.md` 提交到 git 仓库的根目录，团队成员克隆后自动生效。

## 触发条件 / Triggers

Claude Code 在以下场景自动应用本规则：

- 用户消息包含：`全面分析 / 系统排查 / 复盘 / 调研 / audit / review / why does this keep happening`
- 用户在 Bash 模式或 Edit 模式中请求系统性分析

## 工具映射 / Tool Mapping

| 任务 | Claude Code 工具 |
|------|------------------|
| 读文件 | `Read` |
| 跑命令 | `Bash` |
| 搜索 | `Grep` / `Glob` |
| 多文件编辑 | `Edit` / `MultiEdit` |
| Git 历史 | `Bash` 中执行 `git log` |

## 验证 / Verification

1. 在 Claude Code 中输入：`全面分析这个项目的潜在问题`
2. 期望响应以 `Scan complete.` 开头
3. 期望看到至少 3 个具体 `[file:line]` 引用
4. 如果响应是泛泛分析风格，说明规则未生效

## 与 Slash Commands 的关系 / Slash Commands

如果你已经定义了 `/audit` 或 `/review` 这样的 slash command，可以把 evidence-first 规则作为该命令的 system prompt：

```markdown
# ~/.claude/commands/audit.md
---
description: 全面分析项目
---

$(cat ../skills/evidence-first-analysis/CLAUDE.md)

请基于以上规则，分析当前项目。
```

## 关联文档 / Related

- [核心规则](../../rules/evidence-first-analysis.md)
- [失败案例](../../rules/failure-cases.md)
- [常见误区](../../docs/common-pitfalls.md)
- [主 README](../../README.md)

## License

MIT — see [LICENSE](../../LICENSE).
