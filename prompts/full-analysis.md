# Full Analysis Prompt

> Copy this prompt and use it with any LLM Agent.
> 复制这个 prompt 给你正在用的 AI。

## English Version

```
You are about to perform a systematic analysis of a codebase. Follow the
evidence-first rule strictly.

Topic of analysis: <INSERT YOUR TOPIC HERE>
Repository path: <INSERT PATH OR URL>

Required procedure:

1. SCAN FIRST. Before any analysis, invoke tools to:
   - Read the project entry files (package.json, README, AGENTS.md)
   - Read 3-5 source files most relevant to the topic
   - Run any project checks (lint, test, data validation)
   - grep for keywords related to the topic
   - List relevant directories to understand structure

2. OUTPUT STRUCTURE. Begin with:

   Scan complete. Found N real issues (X severe / Y medium / Z minor):
   1. [file:line] Phenomenon / Evidence
   2. [file:line] Phenomenon / Evidence

   Then for each finding, give:
   - Phenomenon (what is wrong)
   - Evidence (verbatim tool output, not paraphrase)
   - Severity (High / Medium / Low)

3. SYNTHESIZE. After findings, give:
   - Systematic causes — each MUST reference at least one finding
   - Fix recommendations — specific to file:line

4. ANTI-PATTERNS. Do NOT:
   - Give 5 reasons before evidence
   - Use abstract best practices disconnected from the code
   - Hide the absence of evidence behind confident language
   - Use pattern matching as a substitute for grep

5. SELF-CHECK before sending:
   - [ ] I invoked at least 3 tools
   - [ ] Every claim references file:line
   - [ ] Causes are synthesized from findings, not training data
   - [ ] Fixes are specific to files

Begin.
```

## 中文版本

```
你即将对代码库进行系统分析。请严格遵守 evidence-first 规则（先证据后归纳）。

分析主题：<在此填入你的主题>
仓库路径：<在此填入路径或 URL>

必须执行：

1. 先扫描。任何分析前必须调用工具：
   - 读项目入口文件（package.json、README、AGENTS.md）
   - 读 3-5 个与主题最相关的源文件
   - 跑项目检查（lint、test、data validation）
   - grep 主题相关关键词
   - 列相关目录结构

2. 输出结构。开头必须是：

   扫描完成，发现 N 个真实问题（X 严重 / Y 中等 / Z 轻微）：
   1. [file:line] 现象 / 证据
   2. [file:line] 现象 / 证据

   每个发现要给出：
   - 现象（错在哪里）
   - 证据（工具原始输出，不要改写）
   - 严重程度（高 / 中 / 低）

3. 归纳。在发现之后：
   - 系统性原因 — 每条必须引用至少一个发现
   - 修复建议 — 具体到 file:line

4. 反模式。不要：
   - 先给 5 条原因再补证据
   - 用脱离代码的抽象最佳实践
   - 用自信语言掩盖缺乏证据的事实
   - 用 pattern matching 代替 grep

5. 发送前自检：
   - [ ] 至少调用了 3 个工具
   - [ ] 每条结论都引用了 file:line
   - [ ] 原因是基于发现归纳的，不是基于训练数据
   - [ ] 修复建议具体到文件

开始。
```

## Usage

1. Copy the version you prefer (English or Chinese)
2. Replace `<INSERT YOUR TOPIC HERE>` with your actual question
3. Replace `<INSERT PATH OR URL>` with the repository you want analyzed
4. Paste the entire prompt to your AI tool
5. The AI will follow the procedure strictly

## Examples of Topics

- "为什么教师卡片的技能标签反复换行"
- "audit the security of the auth flow"
- "review the lazy-loading architecture for data fetching"
- "why does this React component keep re-rendering"
- "全面了解 src/members 目录的数据流"
