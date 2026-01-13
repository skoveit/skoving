# Quick Start

This guide will get you up and running with a private PHOENIX mesh network in minutes.

## Running a Single Node

To start a standard agent node:

```bash
./bin/nostalgia-linux-amd64
```

*Replace the binary name with the one appropriate for your system.*

Upon startup, you should see output indicating the node has started, generated a Peer ID, and is listening for connections.

```text
Host created via tcp with ID: 12D3KooW...
Listening on: /ip4/127.0.0.1/tcp/43210
```

## Creating a Local Mesh

To simulate a mesh network on a single machine, you can run multiple instances of the binary. Since they will use mDNS for local discovery, they should automatically find each other.

1.  **Terminal 1 (Node A):**
    ```bash
    ./bin/nostalgia-linux-amd64
    ```

2.  **Terminal 2 (Node B):**
    ```bash
    ./bin/nostalgia-linux-amd64
    ```

Watch the logs. You should see "Peer Connected" messages appearing in both terminals as they discover each other via mDNS.

## Sending Commands

Currently, the binary supports an interactive mode or CLI flags to send commands.

To check connectivity or run a basic command:

```bash
# This feature depends on the specific implementation details of the CLI (see 'features/commands.md')
help
```

*Note: Specific CLI arguments for issuing commands may vary as the codebase evolves. Refer to the `help` or usage output of the binary.*

## Next Steps

*   Learn about the [Architecture](../architecture/overview.md).
*   Understand [Security](../architecture/security.md).
