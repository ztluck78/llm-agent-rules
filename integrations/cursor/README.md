# Cursor Integration

> Evidence-first rule packaged for Cursor's `.cursorrules` format.
> 把 evidence-first 规则接入 Cursor 编辑器。

## 文件清单 / Files in this directory

| 文件 | 用途 |
|------|------|
| `.cursorrules` | Cursor 的全局规则文件（放在项目根目录或 `~/.cursor/rules/`） |

## 安装 / Installation

### 项目级（推荐 — 仅当前项目生效）

```bash
# 在项目根目录创建 .cursorrules
cp .cursorrules /path/to/your/project/.cursorrules
```

### 用户级（所有项目生效）

```bash
# Cursor 1.0+：放在用户目录
mkdir -p ~/.cursor/rules/
cp .cursorrules ~/.cursor/rules/evidence-first-analysis.md
```

### 通过 Cursor Settings UI

1. 打开 Cursor → `Settings` → `Rules for AI`
2. 粘贴 `.cursorrules` 文件的完整内容
3. 保存

## 触发条件 / Triggers

Cursor 的 `Cmd+K` / `Cmd+L` 行为不会被本规则拦截。规则在以下场景下生效：

- 用户消息包含：`全面分析 / 系统排查 / 复盘 / 调研 / audit / review`
- 用户在 Composer 中请求"全面"分析
- 用户问"为什么这个 bug 反复出现"

## 工具映射 / Tool Mapping

| 任务 | Cursor 工具 |
|------|-------------|
| 读文件 | `@file` 或 `Read` |
| 跑命令 | `Run` 工具 |
| 搜索 | `Grep` / `Codebase Search` |
| 列文件 | `@folder` |

## 验证 / Verification

1. 在 Cursor Composer 中输入：`@codebase 全面分析这个项目`
2. 期望响应以 `Scan complete.` 开头
3. 期望看到至少 3 个 `[file:line]` 引用
4. 如果响应是泛泛的"5 个原因"风格，说明规则未生效

## 常见问题 / FAQ

**Q: 规则会不会影响我正常的代码补全？**
A: 不会。规则只在分析/调研类问题触发，普通 `Tab` 补全不受影响。

**Q: 能否和项目已有的 `.cursorrules` 共存？**
A: 可以。将本文件内容追加到已有的 `.cursorrules` 末尾即可。

**Q: 我用的是 Cursor 0.x（无 Rules UI），怎么办？**
A: 直接把 `.cursorrules` 放在项目根目录，Cursor 0.42+ 自动识别。

## 关联文档 / Related

- [核心规则](../../rules/evidence-first-analysis.md)
- [失败案例](../../rules/failure-cases.md)
- [常见误区](../../docs/common-pitfalls.md)
- [主 README](../../README.md)

## License

MIT — see [LICENSE](../../LICENSE).
