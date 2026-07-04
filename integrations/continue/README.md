# Continue / Cody / Other Integration

> Evidence-first rule packaged for Continue.dev's `.continuerules` format.
> 同时适用于 Sourcegraph Cody 和其他兼容 Continue 格式的工具。
> 同时适配 Continue.dev / Cody / 其他读取 `.continuerules` 的工具。

## 文件清单 / Files in this directory

| 文件 | 用途 |
|------|------|
| `.continuerules` | Continue.dev 的规则文件（同时兼容 Cody） |

## 安装 / Installation

### Continue.dev

#### 项目级（推荐）

```bash
# 在项目根目录创建 .continuerules
cp .continuerules /path/to/your/project/.continuerules
```

#### 用户级（VS Code global storage）

```bash
# Continue.dev 的全局规则目录
mkdir -p ~/.continue/rules/
cp .continuerules ~/.continue/rules/evidence-first-analysis.md
```

### Sourcegraph Cody

```bash
# Cody 同样识别 .continuerules（在某些版本中叫 .cody/rules）
mkdir -p .cody/
cp .continuerules .cody/rules/evidence-first-analysis.md
```

### 其他兼容工具

任何读取项目根目录 `.continuerules` 或类似 `*rules*` 文件的工具都可使用本规则。

## 触发条件 / Triggers

Continue / Cody 在以下场景应用本规则：

- 用户消息包含：`@codebase 全面分析`
- 用户消息包含：`audit / investigate / review / why is this happening`
- 用户在 Chat 面板请求系统性分析

## 工具映射 / Tool Mapping

| 任务 | Continue 工具 | Cody 工具 |
|------|---------------|-----------|
| 读文件 | `readFile` | `file_read` |
| 跑命令 | `runTerminalCommand` | `shell` |
| 搜索 | `grepSearch` / `codebaseSearch` | `rg` / `code_search` |
| 列文件 | `listDir` | `list_dir` |

## 验证 / Verification

1. 在 Continue Chat 中输入：`@codebase 全面分析这个项目`
2. 期望响应以 `Scan complete.` 开头
3. 期望看到至少 3 个具体 `[file:line]` 引用

## Continue 配置示例 / Continue Config Example

```json
// ~/.continue/config.json
{
  "experimental": {
    "rules": [
      {
        "name": "Evidence-First Analysis",
        "rule": "<contents of .continuerules>"
      }
    ]
  }
}
```

## Cody 配置示例 / Cody Config Example

```json
// .vscode/settings.json
{
  "cody.experimental.rules": [
    {
      "name": "Evidence-First Analysis",
      "rule": "<contents of .continuerules>"
    }
  ]
}
```

## 关联文档 / Related

- [核心规则](../../rules/evidence-first-analysis.md)
- [失败案例](../../rules/failure-cases.md)
- [常见误区](../../docs/common-pitfalls.md)
- [主 README](../../README.md)

## License

MIT — see [LICENSE](../../LICENSE).
