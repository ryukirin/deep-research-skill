# Tavily 搜索工具配置

本 Skill 的搜索工具是 **Tavily Search API**。

## 配置 API Key

1. 在 [Tavily](https://app.tavily.com/) 创建 API Key。
2. 将 Key 注入环境变量 `TAVILY_API_KEY`，不要写入仓库文件。
3. 确认运行 Skill 的宿主能够继承该环境变量并发起 HTTPS 请求。

优先使用宿主或操作系统提供的 Secret 管理功能。临时调试时，可以按 [README](../README.md#配置-tavily-api-key) 的方法只为当前终端会话设置变量。

## 调用 Tavily Search API

请求地址：

```text
POST https://api.tavily.com/search
```

认证信息放在 HTTP Header 中：

```text
Authorization: Bearer <TAVILY_API_KEY>
```

不要把 Key 放入请求正文、URL、日志或研究报告。

### PowerShell

```powershell
if (-not $env:TAVILY_API_KEY) {
    throw "TAVILY_API_KEY is not set"
}

$headers = @{
    Authorization = "Bearer $env:TAVILY_API_KEY"
}

$body = @{
    query          = "要检索的问题"
    search_depth   = "basic"
    max_results    = 8
    include_answer = $false
} | ConvertTo-Json

$result = Invoke-RestMethod `
    -Uri "https://api.tavily.com/search" `
    -Method Post `
    -Headers $headers `
    -ContentType "application/json" `
    -Body $body

$result.results
```

### Bash / Zsh

```bash
test -n "$TAVILY_API_KEY" || { echo "TAVILY_API_KEY is not set" >&2; exit 1; }

curl --request POST \
  --url https://api.tavily.com/search \
  --header "Authorization: Bearer $TAVILY_API_KEY" \
  --header "Content-Type: application/json" \
  --data '{
    "query": "要检索的问题",
    "search_depth": "basic",
    "max_results": 8,
    "include_answer": false
  }'
```

## 使用规范

- 默认使用 `search_depth: "basic"`；只有复杂问题确实需要更深检索时才使用 `advanced`。
- 默认将 `include_answer` 设为 `false`，由智能体基于来源自行综合答案。
- 每个子问题至少执行一次检索；关键结论尽量由两个相互独立的来源支持。
- 优先级通常为：官方资料和原始数据 > 同行评审论文 > 权威机构或主流媒体 > 专业二手资料 > 未验证内容。
- 搜索摘要只用于筛选来源，不能替代阅读全文。
- 记录标题、URL、作者或机构、发布日期、访问日期，以及支持结论的章节或段落。
- 遇到来源冲突时并列记录双方证据，不用搜索排名代替事实判断。
- 不在日志、报告、提交记录或错误信息中回显 API Key。

按 Tavily 当前计费说明，`basic` 搜索通常消耗 1 个 API credit，`advanced` 通常消耗 2 个。批量研究前应限制子问题数量和 `max_results`，避免无意义的重复请求。

## 错误处理

- `401` 或 `403`：检查 Key 是否有效、是否被宿主继承，以及 Header 是否使用 `Bearer` 格式。
- `429`：停止连续重试，等待限额恢复，并减少并发和重复查询。
- 网络错误：记录未完成的子问题，说明研究限制，不得伪造检索结果。
- 页面无法访问：寻找公开的一手替代来源，并明确未读取原页面全文。

参考：[Tavily Search API](https://docs.tavily.com/documentation/api-reference/endpoint/search)。
