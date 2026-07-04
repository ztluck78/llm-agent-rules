# GitHub Copilot Integration

> Evidence-first rule packaged for GitHub Copilot's instructions format.
> 把 evidence-first 规则接入 GitHub Copilot。

## 文件清单 / Files in this directory

| 文件 | 用途 |
|------|------|
| `copilot-instructions.md` | Copilot Chat 的自定义指令 |

## 安装 / Installation

### 项目级（推荐 — 仅当前项目生效）

```bash
# 在项目根目录的 .github 目录下创建
mkdir -p .github/
cp copilot-instructions.md .github/copilot-instructions.md
```

### 用户级（所有项目生效）

GitHub Copilot 暂时不支持用户级 instructions 文件。需要逐项目配置。

### 通过 VS Code Settings

1. 打开 VS Code → `Settings` → 搜索 `github.copilot.chat.instructions`
2. 添加 `copilot-instructions.md` 的完整内容

## 触发条件 / Triggers

GitHub Copilot Chat 在以下场景应用本规则：

- 用户在 Chat 中问：`@workspace 全面分析这个项目`
- 用户消息包含：`audit / review / investigate / analyze this code`
- Copilot 自动 @workspace 上下文时

## 工具映射 / Tool Mapping

| 任务 | Copilot 工具 |
|------|--------------|
| 读文件 | `read_file` / `@workspace` |
| 跑命令 | `run_command` |
| 搜索 | `grep_search` / `code_search` |
| 列文件 | `list_dir` |

## 验证 / Verification

1. 在 VS Code 中打开 Copilot Chat
2. 输入：`@workspace 全面分析潜在问题`
3. 期望响应以 `Scan complete.` 开头
4. 期望看到至少 3 个具体 `[file:line]` 引用

## 限制 / Limitations

- **Copilot Completions（行内补全）** 不受本规则影响 — 规则只对 Chat 生效
- **Copilot Workspace** 行为可能略有不同；规则对 Chat 模式最有效
- **Copilot Business/Enterprise** 的组织级 instructions 会覆盖项目级设置

## 关联文档 / Related

- [核心规则](../../rules/evidence-first-analysis.md)
- [失败案例](../../rules/failure-cases.md)
- [常见误区](../../docs/common-pitfalls.md)
- [主 README](../../README.md)

## License

MIT — see [LICENSE](../../LICENSE).
