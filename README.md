![这是图片](./images/title.png)

<div align="center">

**Codex 使用的 Gemini MCP 服务器**

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](./LICENSE) [![Python Version](https://img.shields.io/badge/python-3.12+-blue.svg)](https://www.python.org/downloads/) [![MCP Compatible](https://img.shields.io/badge/MCP-Compatible-green.svg)](https://modelcontextprotocol.io)

[English](./docs/README_EN.md) | 简体中文

</div>

---

## 项目简介

**Gemini-MCP** 是一个 MCP 服务器，用于在 Codex 中以工具方式调用本地 Gemini CLI。它负责启动 Gemini CLI、读取 stream-json 输出并聚合为 MCP 返回结构，便于会话续接与结果消费。

## 主要特性

- 标准 MCP 工具接口：`gemini`
- stdio 传输，适配 Codex 的 MCP 客户端
- 会话续接：返回 `SESSION_ID`
- 可选 `sandbox` 运行模式
- 跨平台（Windows/WSL/Linux）

## 快速开始

### 前置要求

- 已安装并配置 Gemini CLI
- 已安装 uv
- Python 3.12+

### 在 Codex 中注册 MCP 服务

在 Codex 的 MCP 配置中新增一个 stdio 服务，启动命令如下：

```bash
uvx --from git+https://github.com/pickarm/codex-gemini-mcp.git geminimcp
```

如需固定版本，请将 `git+...` 替换为具体 tag 或 commit。

也可以用配置文件方式（字段名以你当前 Codex 版本为准）：

```json
{
  "mcpServers": {
    "gemini": {
      "command": "uvx",
      "args": [
        "--from",
        "git+https://github.com/pickarm/codex-gemini-mcp.git",
        "geminimcp"
      ]
    }
  }
}
```

### 本地运行（可选）

```bash
git clone https://github.com/pickarm/codex-gemini-mcp.git
cd "codex-gemini-mcp"
uv sync
uv run geminimcp
```

或：

```bash
uv run python -m geminimcp.cli
```

### Windows 安装与配置

Windows 无需单独版本，流程与其他平台一致：

1. 安装并配置 Gemini CLI，确保 `gemini` 在 PATH 中：
   - `where gemini`
2. 安装 uv，确保 `uv`/`uvx` 在 PATH 中：
   - `uv --version`
3. 使用上面的 `uvx` 命令注册 MCP；`cd` 路径建议使用正斜杠并保持绝对路径。

## 工具说明

### gemini 参数

| 参数 | 类型 | 必填 | 默认值 | 说明 |
|------|------|------|--------|------|
| `PROMPT` | `str` | 是 | - | 发送给 Gemini 的任务指令 |
| `cd` | `path` | 是 | - | Gemini CLI 的工作目录/项目根目录 |
| `sandbox` | `bool` | 否 | `False` | 是否启用沙箱模式 |
| `SESSION_ID` | `str` | 否 | `""` | 会话 ID（空则开启新会话） |
| `return_all_messages` | `bool` | 否 | `False` | 是否返回完整消息记录 |
| `model` | `str` | 否 | `""` | 指定模型（默认使用 Gemini CLI 配置） |

建议 `cd` 使用绝对路径并使用正斜杠，例如：

```json
{
  "PROMPT": "用一句话解释 MCP。",
  "cd": "D:/ai/workspace"
}
```

继续会话示例：

```json
{
  "PROMPT": "继续刚才的话题。",
  "cd": "D:/ai/workspace",
  "SESSION_ID": "session-uuid"
}
```

### 返回值结构

**成功时：**
```json
{
  "success": true,
  "SESSION_ID": "session-uuid",
  "agent_messages": "Gemini 的回复内容..."
}
```

**启用 return_all_messages 时额外包含：**
```json
{
  "all_messages": [...]
}
```

**失败时：**
```json
{
  "success": false,
  "error": "错误信息描述"
}
```

## 安全与隐私

- 本项目仅调用本地 Gemini CLI，不保存或记录凭据。
- Gemini 的认证与网络行为由 Gemini CLI 自身配置与管理。

## 开发与贡献

```bash
git clone https://github.com/pickarm/codex-gemini-mcp.git
cd "codex-gemini-mcp"
uv sync
```

本地运行：
```bash
uv run geminimcp
```

## 常见问题

<details>
<summary>Q1: gemini 命令不可用或找不到？</summary>

请确认已按 Gemini CLI 官方文档完成安装与认证，并确保 `gemini` 在 PATH 中可用。Windows 上建议重启终端后再试。

</details>

<details>
<summary>Q2: 为什么没有拿到 SESSION_ID？</summary>

本工具依赖 Gemini CLI 的 `stream-json` 输出。如果 CLI 输出被拦截或格式异常，可能导致无法解析会话信息。

</details>

## 许可证

本项目采用 [MIT License](./LICENSE)。
