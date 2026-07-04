# Aider Integration

> Evidence-first rule packaged for Aider's `CONVENTIONS.md` format.
> 把 evidence-first 规则接入 Aider 终端 AI 编码助手。

## 文件清单 / Files in this directory

| 文件 | 用途 |
|------|------|
| `CONVENTIONS.md` | Aider 的项目级约定文件 |

## 安装 / Installation

### 项目级（推荐）

```bash
# 在项目根目录创建 CONVENTIONS.md
cp CONVENTIONS.md /path/to/your/project/CONVENTIONS.md

# Aider 自动识别
aider
```

### 通过命令行参数（一次性使用）

```bash
aider --read CONVENTIONS.md
```

### 通过 Aider 配置文件（`.aider.conf.yml`）

```yaml
# .aider.conf.yml
read: CONVENTIONS.md
```

## 触发条件 / Triggers

Aider 在以下场景应用本规则：

- 用户输入：`/audit` 或 `/review` 命令
- 用户消息包含：`全面分析 / 复盘 / investigate / why does this keep breaking`
- Aider 检测到用户在做多文件改动时

## 工具映射 / Tool Mapping

| 任务 | Aider 命令 |
|------|-----------|
| 读文件 | 自动（被 `--read` 加载的文件 + 用户指定的文件） |
| 跑命令 | `!` 前缀（如 `!npm test`） |
| 搜索 | Aider 内部 grep |
| Git 操作 | `/commit` / `/undo` 等 |

## 验证 / Verification

1. 在 Aider 中输入：`/audit src/`
2. 期望响应以 `Scan complete.` 开头
3. 期望看到至少 3 个具体 `[file:line]` 引用

## Aider 特定说明 / Aider-Specific Notes

- Aider 的 **commit message** 风格与本规则的"具体修改文件"哲学一致 — 充分利用
- Aider 的 **architect mode**（`--architect`）会做更系统的分析；evidence-first 规则与 architect 模式协同良好
- Aider 的 **voice mode** 不应被规则干扰（语音输入可能不符合触发关键词）

## 关联文档 / Related

- [核心规则](../../rules/evidence-first-analysis.md)
- [失败案例](../../rules/failure-cases.md)
- [常见误区](../../docs/common-pitfalls.md)
- [主 README](../../README.md)

## License

MIT — see [LICENSE](../../LICENSE).
