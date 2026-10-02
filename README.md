# github-mcp-server (for Operit)

把 **GitHub 官方 MCP Server** 接进 [Operit](https://github.com/AAswordman/Operit) 的配置条目。

> 本仓只提供「Operit 侧的接入配置与说明」；MCP 服务本体是 GitHub 官方的远端服务：
> https://github.com/github/github-mcp-server

## 特性

- **远端直连**：不用 Docker、不用装 Node/Python，配置好即可用
- **46 个工具**：仓库 / 文件 / Issue / PR / 代码搜索 / Release / 密钥扫描等
- **官方端点**：`https://api.githubcopilot.com/mcp/`，请求直连 GitHub，不经第三方

## 安装步骤

1. 在 Operit 市场安装本插件（或手动把 `mcp_config.json` 内容合并进 `mcp_config.json`）
2. 把配置里的 `bearerToken` 占位符 `<YOUR_GITHUB_PAT>` 换成你自己的 GitHub Personal Access Token
3. 重启 MCP 服务
4. 验证：让 AI 执行 `get_me`，能返回你的账号信息即成功

## 生成 PAT

推荐 **Fine-grained token**（GitHub → Settings → Developer settings → Personal access tokens → Fine-grained tokens）：

| 权限 | 推荐值 |
|---|---|
| Repository access | 只勾需要操作的仓库 |
| Contents | Read and write |
| Issues | Read and write |
| Pull requests | Read and write |
| Metadata | Read-only（自动带上） |

备选 Classic token：只需勾 `repo`（需要组织团队功能再加 `read:org`）。
❗ 不要勾 `admin:org`、`delete_repo` 这类高权范围。

## 可选：收窄端点

| 用途 | 端点 |
|---|---|
| 全量 46 工具 | `https://api.githubcopilot.com/mcp/` |
| 只读 | `https://api.githubcopilot.com/mcp/readonly` |
| 仅仓库类 | `https://api.githubcopilot.com/mcp/x/repos` |
| 仅 Issue | `https://api.githubcopilot.com/mcp/x/issues` |
| 仅 PR | `https://api.githubcopilot.com/mcp/x/pull_requests` |

## 常见问题

| 现象 | 修法 |
|---|---|
| 工具列表为空 / 加载失败 | 检查 `bearerToken` 是否填了、首尾有无空格 |
| 401 | Token 过期或已撤销，重新生成 |
| 403 | 权限不足，给 token 加上 Contents / Issues / Pull requests |
| 重启后插件看不见 | 需存在目录 `mcp_plugins/<id>/`，且条目补齐 installedPath / installedTime / logoUrl / longDescription / updatedAt 字段 |

## 安全声明

Token 仅保存在你本机；本仓不含任何令牌。请勿把 Token 提交到任何仓库。

## 许可

本仓的配置文件与文档以 MIT 提供；MCP 服务本身归 GitHub 所有。
