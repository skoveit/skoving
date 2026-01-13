# Commands & Execution

The PHOENIX Controller provides a powerful CLI for interacting with the mesh.

## Interactive Mode

When you start the binary (as a controller), you enter an interactive shell.

```text
Connected to agent
Type 'help' for commands, TAB for completion

[0 peers]> 
```

## Command Reference

### Basic Commands

| Command | Description | Usage |
| :--- | :--- | :--- |
| `help` | Show available commands. | `help` |
| `peers` | List all currently connected peers. | `peers` |
| `id` | Show the local node's Peer ID. | `id` |
| `sign` | Authenticate as operator. | `sign <key>` |
| `cls` / `clear` | Clear the terminal screen. | `cls` |
| `quit` / `exit` | Shutdown the node and exit. | `quit` |

### Interaction Commands

| Command | Description | Usage |
| :--- | :--- | :--- |
| `use` | Select a specific peer to target. | `use <peer-id>` (Use TAB for completion) |
| `back` | Deselect the current peer. | `back` |
| `run` | Execute a shell command on the *selected* peer. | `run <shell-command>` |
| `send` | One-shot command to a specific peer. | `send <peer-id> <shell-command>` |

### Network Visualization

| Command | Description | Usage |
| :--- | :--- | :--- |
| `radar` | Scan the network and measure latency to all reachable nodes. | `radar` |
| `graph` | Start/Stop the web-based topology visualizer. | `graph on` / `graph off` |

## Example: Remote Execution

1.  **List Peers** to find a target.
    ```bash
    [0 peers]> peers
    Connected peers:
      1. 12D3KooWABC...
      2. 12D3KooWXYZ...
    ```

2.  **Select a Peer**.
    ```bash
    [0 peers]> use 12D3KooWABC...
    Selected peer: 12D3KooWABC...
    [12D3KooW...]> 
    ```

3.  **Run a Command**.
    ```bash
    [12D3KooW...]> run whoami
    root
    ```

4.  **Deselect**.
    ```bash
    [12D3KooW...]> back
    Deselected peer
    [2 peers]> 
    ```
