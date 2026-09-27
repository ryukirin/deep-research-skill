# 搜索工具配置

## 工具选择

1. 优先使用运行环境提供的原生 `websearch` 和 `webfetch`。`websearch` 用于发现来源，`webfetch` 用于读取指定页面。
2. 若原生 WebSearch 不可用，但允许执行网络请求，可直接调用 Tavily Search API。
3. 如果无法联网，不得凭记忆伪造检索结果；说明限制，并请用户提供材料或启用搜索。

## OpenCode WebSearch

OpenCode v2 支持 Exa、Firecrawl、Parallel 和 Tavily。可在 TUI 中执行 `/connect`，或在启动 OpenCode 前设置对应环境变量：

| Provider | 环境变量 |
| --- | --- |
| Exa | `EXA_API_KEY` |
| Firecrawl | `FIRECRAWL_API_KEY` |
| Parallel | `PARALLEL_API_KEY` |
| Tavily | `TAVILY_API_KEY` |

指定 Tavily：

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "websearch": {
    "provider": "tavily"
  }
}
```

旧版 OpenCode 可在启动前设置 `OPENCODE_ENABLE_EXA=1` 使用托管 Exa 搜索；该模式通常不要求用户提供 Exa API Key。

## Tavily API Key

从 [Tavily 控制台](https://app.tavily.com/) 创建 Key，并只通过环境变量提供。不要把真实 Key 写入仓库、配置示例、命令历史截图或研究报告。

```powershell
# Windows PowerShell：当前终端
$env:TAVILY_API_KEY = "你的-Tavily-Key"
```

```bash
# macOS / Linux：当前终端
export TAVILY_API_KEY="你的-Tavily-Key"
```

不要打印变量值。只检查变量是否存在：

```powershell
if ($env:TAVILY_API_KEY) { "TAVILY_API_KEY is set" }
```

## 直接调用 Tavily REST API

仅在没有原生 WebSearch 且网络请求已获允许时使用：

```powershell
if (-not $env:TAVILY_API_KEY) {
    throw "TAVILY_API_KEY is not set"
}

$body = @{
    api_key      = $env:TAVILY_API_KEY
    query        = "要检索的子问题"
    search_depth = "advanced"
    max_results  = 8
} | ConvertTo-Json

Invoke-RestMethod `
    -Uri "https://api.tavily.com/search" `
    -Method Post `
    -ContentType "application/json" `
    -Body $body
```

## 使用规范

- 每个子问题至少执行一次检索；关键结论尽量由两个相互独立的来源支持。
- 优先级通常为：官方资料和原始数据 > 同行评审论文 > 权威机构或主流媒体 > 专业二手资料 > 未验证内容。
- 搜索摘要只用于筛选来源，不能替代阅读全文。
- 记录标题、URL、作者或机构、发布日期、访问日期，以及支持结论的章节或段落。
- 遇到来源冲突时并列记录双方证据，不用搜索排名代替事实判断。
- 不在日志、报告、提交记录或错误信息中回显 API Key。

参考：[OpenCode Websearch](https://opencode.ai/v2/docs/websearch)。
