# Deep Research Skill

一个面向 OpenCode 的可复用深度研究 Skill。输入研究问题后，它会按“元思考 → 问题拆解 → 资料检索 → 来源精读 → 证据综合 → 审校发布”的流程，输出带可追溯引用的结构化中文报告。

> [!IMPORTANT]
> 本项目是学习 [Gitconomy Research / Git4GenThinking](https://www.gitlink.org.cn/Gitconomy/Git4GenThinking) 课程后完成的学习产物和衍生实践，并非课程官方项目，也不代表原作者或社区背书。

## 来源与许可

- 学习来源：[Gitconomy Research / Git4GenThinking](https://www.gitlink.org.cn/Gitconomy/Git4GenThinking)
- 原课程版权：© 2025–2026 Gitconomy Research 社区
- 原课程许可：CC BY-SA 4.0
- 本项目基于课程中的 Deep Research 六步流程、Karpathy 四原则和 Skill 封装方法进行整理，并补充了独立安装、网页搜索配置、密钥安全说明和报告模板。
- 本项目作为衍生作品，同样采用 [CC BY-SA 4.0](LICENSE) 许可。

## 目录

```text
deep-research-skill/
├── SKILL.md                     # Skill 入口与执行规则
├── workflows/research-flow.md   # 六步研究流程
├── tools/search.md              # WebSearch / Tavily 配置与使用规范
└── references/report-template.md
```

## 安装

前置条件：已安装并配置 OpenCode，且具备可用的网页搜索工具。

### macOS / Linux

```bash
git clone https://github.com/ryukirin/deep-research-skill.git \
  ~/.opencode/skills/deep-research-skill
```

### Windows PowerShell

```powershell
git clone https://github.com/ryukirin/deep-research-skill.git `
  "$env:USERPROFILE\.opencode\skills\deep-research-skill"
```

安装后重启 OpenCode。在对话中说“加载 deep-research-skill”，或直接提出一个需要多来源研究和证据追踪的问题。

## 配置网页搜索

推荐使用 OpenCode 内置 `websearch`。OpenCode v2 支持 Exa、Firecrawl、Parallel 和 Tavily；也可以在 TUI 中执行 `/connect` 连接搜索服务。下面以 Tavily 为例。

### 1. 获取 Tavily API Key

在 [Tavily](https://app.tavily.com/) 注册并创建 API Key。不要把真实 Key 写进本仓库、`SKILL.md`、聊天记录或截图。

### 2. 设置环境变量

只对当前终端生效：

```powershell
# Windows PowerShell
$env:TAVILY_API_KEY = "你的-Tavily-Key"
opencode
```

```bash
# macOS / Linux
export TAVILY_API_KEY="你的-Tavily-Key"
opencode
```

Windows 持久保存到当前用户环境变量：

```powershell
[Environment]::SetEnvironmentVariable(
  "TAVILY_API_KEY",
  "你的-Tavily-Key",
  "User"
)
```

执行后请关闭并重新打开终端，再启动 OpenCode。可用以下命令确认变量已经存在；这些命令不会打印 Key 本身：

```powershell
# Windows PowerShell
if ($env:TAVILY_API_KEY) { "TAVILY_API_KEY is set" }
```

```bash
# macOS / Linux
test -n "$TAVILY_API_KEY" && echo "TAVILY_API_KEY is set"
```

### 3. 指定 Tavily 为 WebSearch Provider（OpenCode v2）

在项目的 `opencode.jsonc` 中加入：

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "websearch": {
    "provider": "tavily"
  }
}
```

首次搜索时，OpenCode 也可能提示选择 Provider。其他支持的环境变量为 `EXA_API_KEY`、`FIRECRAWL_API_KEY` 和 `PARALLEL_API_KEY`。

旧版 OpenCode 可通过 `OPENCODE_ENABLE_EXA=1` 启用托管 Exa WebSearch，通常不需要单独的 Exa API Key：

```powershell
$env:OPENCODE_ENABLE_EXA = "1"
opencode
```

更完整的工具选择和直接调用 Tavily REST API 的方法见 [tools/search.md](tools/search.md)。OpenCode 配置以官方的 [Websearch 文档](https://opencode.ai/v2/docs/websearch) 为准。

## 使用示例

```text
请使用 deep-research-skill 研究：
“RAG 与长上下文在企业知识库中的取舍是什么？”

关注：准确性、成本、延迟、数据更新和权限控制。
优先来源：官方文档、论文和权威机构报告。
```

最终输出包含：

1. 研究结论：每条结论都能追溯到来源；
2. 资料来源：记录标题、链接及章节或段落位置；
3. 待确认事项：明确证据不足、来源冲突和时效边界。

## 安全说明

- 仓库不包含任何真实 API Key、Token、Cookie 或账号凭据。
- `.env`、本地 OpenCode 状态和运行时研究产物已加入 `.gitignore`。
- 发布包未包含示例研究报告、原工作区的 `node_modules`、个人研究记录、Git 历史或其他项目文件。
- 如果 Key 曾经提交到 Git，即使后来删除，也应立即在服务商后台吊销并重新生成；仅删除文件不能清除 Git 历史。

## 已知限制

- 研究质量取决于可访问的来源与所用模型；付费墙和登录内容可能无法读取。
- 对新闻、价格、法规等时效性强的信息，应在报告中标明检索日期。
- 自动生成的结论仍需使用者复核，不应直接替代专业判断。
