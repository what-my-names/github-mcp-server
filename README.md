# github-mcp-server (for Operit)

把 **GitHub 官方 MCP Server** 接进 [Operit](https://github.com/AAswordman/Operit) 的配置条目。

> 本仓只提供「Operit 侧的接入配置与说明」；MCP 服务本体是 GitHub 官方的远端服务：
> https://github.com/github/github-mcp-server

## 特性

- **远端直连**：不用 Docker、不用装 Node/Python，配置好即可用
- **46 个工具**：仓库 / 文件 / Issue / PR / 代码搜索 / Release / 密钥扫描等
- **官方端点**：`https://api.githubcopilot.com/mcp/`，请求直连 GitHub，不经第三方

## 安装步骤

**方式 A——市场一键安装（推荐）**

1. 在 Operit 市场安装本插件（内部走标准配置导入）
2. 打开插件 `github_mcp` → 编辑 → 远程连接里确认 endpoint：`https://api.githubcopilot.com/mcp/`
3. 在 **Bearer Token** 里填自己的 GitHub PAT（`<YOUR_GITHUB_PAT>` 是占位符，不能直接用）
4. 重启 MCP 服务 → 让 AI 执行 `get_me`，能返回你的账号信息即成功

**方式 B——手动合并配置**

1. 把本仓 `mcp_config.json` 的 `pluginMetadata` 条目合并进你本机的
   `Download/Operit/mcp_plugins/mcp_config.json`
2. 同时建目录 `mcp_plugins/github_mcp/`（空目录也行）
3. 把 `bearerToken` 的占位符换成自己的 PAT，重启 MCP 服务

> 两种方式都只需替换占位符。仓库内**不含任何真实令牌**。

## 配置文件说明

| 文件 | 用途 | 格式 |
|---|---|---|
| `market_install_config.json` | 市场发布 / App 内「从 JSON 导入」 | 标准 MCP：根节点 `mcpServers`，远端项用 `type: streamable_http` + `url` |
| `mcp_config.json` | 手动合并进本机配置 | Operit 本地文件格式：`pluginMetadata` 条目 |

⚠️ 两个容易踩的点（源码实证）：

- 市场/导入路径**只解析 `mcpServers`**，写成空的会报「mcpServers 不能为空」；
- 导入时 `bearerToken` 会被清空，鉴权头以 `headers` 为准——所以仓库里的标准配置
  `headers` 故意留空，避免占位符盖掉你在界面里填的真 Token。

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
| 安装时报「mcpServers 不能为空」 | 旧版市场配置只写了 `pluginMetadata`；应从市场安装最新版本，或用 `market_install_config.json` 导入 |

## 安全声明

Token 仅保存在你本机；本仓不含任何令牌。请勿把 Token 提交到任何仓库。

## 许可

本仓的配置文件与文档以 MIT 提供；MCP 服务本身归 GitHub 所有。
