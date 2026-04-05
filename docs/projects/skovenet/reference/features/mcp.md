# MCP Server Integration

The SkoveNet controller ships with a built-in [Model Context Protocol (MCP)](https://modelcontextprotocol.io) server, allowing AI assistants such as **Claude Desktop**, **Cursor**, and any other MCP-compatible client to interact with the network directly.

## How It Works

The MCP server uses the standard **stdio transport** (JSON-RPC 2.0 over stdin/stdout). When launched with the `--mcp` flag, the controller replaces the interactive CLI with a machine-readable protocol session. The controller must be connected to a running local agent.

```
AI Client (Claude Desktop, etc.)
        │  JSON-RPC 2.0 over stdio
        ▼
  controller --mcp
        │  Unix socket IPC
        ▼
     agent (local)
        │  P2P mesh
        ▼
  remote peers
```

## Usage

### Starting the MCP Server

The agent must be running first. Then start the controller in MCP mode:

```bash
./controller --mcp
```

The process will read JSON-RPC requests from stdin and write responses to stdout — the client manages the process lifecycle.

### Claude Desktop Configuration

Add to your `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "skovenet": {
      "command": "/path/to/controller",
      "args": ["--mcp"]
    }
  }
}
```

After restarting Claude Desktop, SkoveNet tools will appear in the tools panel.

## Available Tools

| Tool | Description |
|---|---|
| `get_node_id` | Returns the local agent's node ID |
| `list_peers` | Lists all peer IDs currently connected to the mesh |
| `use_peer` | Sets the active peer target for subsequent commands |
| `send_command` | Executes a shell command on a peer and returns output |
| `radar_scan` | Scans the network for reachable nodes and returns latency results |

### Tool Details

#### `get_node_id`
Returns the local node's libp2p Peer ID. No arguments required.

#### `list_peers`
Returns a formatted list of all peers currently connected to the local agent. No arguments required.

#### `use_peer`
Sets the default target peer for `send_command` calls.

| Argument | Type | Required | Description |
|---|---|---|---|
| `peer_id` | string | ✅ | Full peer ID to set as active target |

Returns an error if the peer is not found in the current peer list.

#### `send_command`
Executes a command on a remote peer via the mesh.

| Argument | Type | Required | Description |
|---|---|---|---|
| `command` | string | ✅ | Shell command to execute (e.g. `whoami`, `ls /tmp`) |
| `peer_id` | string | ❌ | Override peer; uses active peer from `use_peer` if omitted |
| `timeout_sec` | integer | ❌ | Seconds to wait for output (default: `15`) |

#### `radar_scan`
Initiates a 3-second network scan and returns all discovered nodes sorted by latency, including signal quality ratings (EXCELLENT / GOOD / FAIR / WEAK / POOR). No arguments required.

## Example Session

A typical AI-assisted recon flow:

1. **`get_node_id`** → confirm I'm connected to the right agent
2. **`list_peers`** → see available targets
3. **`use_peer`** with a chosen peer ID
4. **`send_command`** `whoami` → confirm identity on target
5. **`send_command`** `ls /home` → browse the filesystem
6. **`radar_scan`** → discover additional nodes

## Notes

- The `--mcp` flag is mutually exclusive with the interactive CLI; there is no shared state between MCP sessions and CLI sessions.
- Each MCP session maintains its own active peer selection, independent of other sessions.
- The agent must already be running before launching the controller in MCP mode.
