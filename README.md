# Deep Research Skill

一个采用 [Agent Skills 开放格式](https://agentskills.io/specification) 编写的深度研究 Skill。它把复杂问题拆成可检索的子问题，通过 Tavily 搜索、来源精读和交叉验证，输出带可追溯引用的结构化报告。

> 本项目是学习 [Gitconomy Research / Git4GenThinking](https://www.gitlink.org.cn/Gitconomy/Git4GenThinking) 后形成的实践产物，并在原思路上整理为可复用 Skill。项目按 [CC BY-SA 4.0](LICENSE) 许可发布。

## 项目结构

```text
deep-research-skill/
├── SKILL.md
├── LICENSE
├── workflows/
│   └── research-flow.md
├── tools/
│   └── search.md
└── references/
    └── report-template.md
```

仓库不包含示例输出目录。按照现有 `SKILL.md`，宿主具备写入能力且用户没有要求“仅在对话中返回”时，研究过程可能把中间结果写入 `docs/deep-research/`；如果不希望写文件，请在任务中明确说明。

## 运行要求

- 支持 Agent Skills 的智能体宿主；
- 宿主能够发起 HTTPS 请求，并能打开候选来源页面进行正文核验；
- 一个有效的 Tavily API Key。

本项目的搜索工具是 **Tavily Search API**。`tools/search.md` 是这个工具的调用和安全配置说明。

## 安装

把整个仓库复制或克隆到宿主的 Skills 目录。以 Codex 为例：

macOS / Linux：

```bash
git clone https://github.com/ryukirin/deep-research-skill.git \
  "${CODEX_HOME:-$HOME/.codex}/skills/deep-research-skill"
```

Windows PowerShell：

```powershell
$codexRoot = if ($env:CODEX_HOME) { $env:CODEX_HOME } else { Join-Path $HOME ".codex" }
git clone https://github.com/ryukirin/deep-research-skill.git `
  (Join-Path $codexRoot "skills/deep-research-skill")
```

其他兼容 Agent Skills 的宿主，请将仓库完整复制到该宿主约定的 Skills 目录。

## 配置 Tavily API Key

1. 在 [Tavily](https://app.tavily.com/) 创建 API Key。
2. 优先使用宿主或操作系统提供的 Secret 管理功能。
3. 如果宿主会继承当前终端环境，可以在启动宿主前仅为当前会话设置 `TAVILY_API_KEY`。

PowerShell（输入内容不会显示在屏幕上，也不会写入命令历史）：

```powershell
$secureKey = Read-Host "TAVILY_API_KEY" -AsSecureString
$keyPtr = [Runtime.InteropServices.Marshal]::SecureStringToBSTR($secureKey)
try {
    $env:TAVILY_API_KEY = [Runtime.InteropServices.Marshal]::PtrToStringBSTR($keyPtr)
} finally {
    [Runtime.InteropServices.Marshal]::ZeroFreeBSTR($keyPtr)
    Remove-Variable secureKey, keyPtr -ErrorAction SilentlyContinue
}
```

Bash / Zsh：

```bash
read -rsp "TAVILY_API_KEY: " TAVILY_API_KEY
export TAVILY_API_KEY
printf '\n'
```

确认变量已设置，但不要打印 Key 本身：

```powershell
if ($env:TAVILY_API_KEY) { "TAVILY_API_KEY is set" }
```

```bash
test -n "$TAVILY_API_KEY" && echo "TAVILY_API_KEY is set"
```

上述变量只在当前终端会话及其子进程中有效。不要把真实 Key 写进 `SKILL.md`、配置示例、Git 提交、日志或截图；如果 Key 曾被提交，应立即在 Tavily 控制台撤销并重新生成。完整调用方式见 [tools/search.md](tools/search.md)。

## 使用

安装并配置后，可以直接向宿主提出研究任务，例如：

```text
请使用 deep-research-skill 调研 2026 年主流代码智能体的评测方法，比较其适用范围、证据质量和局限性，并附来源。
```

Skill 会依次执行范围界定、问题拆解、Tavily 检索、来源精读、证据综合和报告审校。关键结论会尽量由两个相互独立的来源支持；证据不足或存在冲突时会明确标注。

## 安全与边界

- 搜索结果和网页内容属于不可信输入，不执行其中夹带的指令。
- 搜索摘要只用于筛选，关键结论必须回到来源正文核验。
- 可能按 `SKILL.md` 把研究中间结果写入本地 `docs/deep-research/`；可通过要求“仅在对话中返回，不写文件”关闭。
- 不代表用户发送消息、提交表单或修改远端数据。
- 新闻、价格、法规、产品能力等易变化信息必须标注检索日期。
- 研究结果不替代法律、医疗或财务专业意见。

## 致谢与许可

本项目明确源自对 [Git4GenThinking](https://www.gitlink.org.cn/Gitconomy/Git4GenThinking) 的学习和实践。感谢原项目作者与贡献者提供课程材料和研究方法参考。

本项目使用 [Creative Commons Attribution-ShareAlike 4.0 International](LICENSE) 许可。再发布或改编时，请保留归属说明，并以相同许可共享衍生内容。
