# 🚀 OrbStack MCP Tools

A collection of lightweight ZSH functions to manage **Model Context Protocol (MCP)** servers inside Docker containers.

Optimized for **macOS + OrbStack**, this toolkit allows you to run any MCP server via `npx` without installing Node.js or npm on your host machine.

## ✨ Features

- **Host Cleanliness**: No Node.js/npm required on your Mac. Everything runs in isolated `node:alpine` containers.
- **OrbStack Magic**: Automatic local domains (e.g., `http://orb.local`) for HTTP/SSE servers.
- **Dual Mode Support**:
  - **STDIO**: For Claude Desktop (container starts and stops automatically with the client).
  - **SSE (HTTP)**: For LM Studio and background tasks (always available via local URL).
- **Auto-Config**: Generates ready-to-use JSON blocks for Claude Desktop or LM Studio settings.

## 📦 Installation

Run this command in your terminal:

```bash
curl -s https://raw.githubusercontent.com/nut-code-monkey/mcp-tools/refs/heads/main/mcp-tools -o ~/.mcp-tools && source ~/.mcp-tools
```

Once installed, reload your shell environment:
```bash
source ~/.zshrc
```

## 🛠 Usage

### 1. STDIO Mode
Use `mcp-create` to generate the configuration. The container will be managed by Claude Desktop/LL Studio.

```bash
mcp-create my-server npx -y @modelcontextprotocol/server-everything
```
Output MCP configuration:
```json
{
    "mcpServers": {
        "my-server": {
            "command": "zsh",
            "args": ["-c", "source ~/.zshrc && mcp-run my-server npx -y @modelcontextprotocol/server-everything"]
        }
    }
}
```
Copy it into MCP `config.json`.

### 2. SSE Mode
Use `mcp-create-http`. The container will run in the background with an `unless-stopped` restart policy.

```bash
mcp-create-http my-server 3001 npx -y @modelcontextprotocol/server-everything sse
```
*Your server will be available at: `http://orb.local`*
Output MCP configuration:
```json
{
    "mcpServers": {
        "my-server": {
            "type": "sse",
            "url": "http://my-server.orb.local:3001/sse"
        }
    }
}
```

## 📑 Core Commands

| Command | Description |
| :--- | :--- |
| `mcp-create <name> <command>` | Generates STDIO JSON config |
| `mcp-create-http <name> [port] <command>` | Starts SSE server in background and provides URL/JSON |

## ⚙️ Requirements

- **macOS**
- **OrbStack** (recommended) or Docker Desktop

## 📝 Important Notes

1. **File Access**: The current working directory from which you run the command is mounted to `/app` inside the container.
2. **Environment Variables**: For STDIO containers, you can manually add API keys to the `"env"` section of the generated JSON.
3. **Persistence**: Containers launched via `mcp-create-http` will automatically restart after a system reboot thanks to OrbStack/Docker policies.
