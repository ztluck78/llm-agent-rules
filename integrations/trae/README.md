# Trae IDE Integration

> 把 evidence-first 规则接入 Trae IDE 的本地化版本。
> Trae-native version of the evidence-first rule.

## 文件清单 / Files in this directory

| 文件 | 用途 |
|------|------|
| `SKILL.md` | Trae Skill 格式的规则文件（带 frontmatter） |

## 安装 / Installation

### 全局安装（推荐 — 所有项目生效）

```bash
# 1. 确认 Trae Skill 目录存在
mkdir -p ~/.trae-cn/builtin/global/skills/

# 2. 复制 SKILL.md 到全局目录
cp SKILL.md ~/.trae-cn/builtin/global/skills/evidence-first-analysis/SKILL.md

# 3. 重启 Trae IDE
```

### 项目级安装（仅当前项目生效）

```bash
# 在项目根目录下创建 .trae/skills/ 目录
mkdir -p .trae/skills/evidence-first-analysis/

# 复制文件
cp SKILL.md .trae/skills/evidence-first-analysis/SKILL.md
```

## 触发条件 / Triggers

Trae 在以下场景自动加载本 Skill：

- 用户消息包含：`全面分析 / 系统排查 / 复盘 / 调研 / 全面了解`
- 用户消息包含：`audit / investigation / review / assess / deep dive`
- 用户消息包含：`为什么反复出这个问题 / 这个 bug 怎么产生的`

## Trae 工具映射 / Tool Mapping

| 任务 | Trae 工具 |
|------|-----------|
| 读文件 | `Read` |
| 跑命令 | `RunCommand` |
| 搜索文本 | `Grep` |
| 列文件 | `Glob` |
| 多文件编辑 | （参见 Trae 文档） |

## 验证 / Verification

1. 在 Trae 中问：`全面分析这个项目的代码质量`
2. 期望看到响应以 `Scan complete.` 开头
3. 期望看到至少 3 个具体 `[file:line]` 引用
4. 如果响应是泛泛的"5 个原因"风格，说明 Skill 未生效

## 卸载 / Uninstallation

```bash
rm -rf ~/.trae-cn/builtin/global/skills/evidence-first-analysis/
```

## 关联文档 / Related

- [核心规则](../../rules/evidence-first-analysis.md)
- [失败案例](../../rules/failure-cases.md)
- [设计原理](../../rules/design-rationale.md)
- [主 README](../../README.md)

## License

MIT — see [LICENSE](../../LICENSE).
