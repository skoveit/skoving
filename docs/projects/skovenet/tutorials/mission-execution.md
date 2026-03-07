# Mission Execution

Once you have generated your agents, this guide covers deployment, mesh formation, and command execution.

## Deployment

Transfer the generated agent binary to the target machine and execute it.

```bash
./agent-linux-amd64
```

The agent will start, generate its cryptographic identity (Peer ID), and begin listening for connections.

## Forming the Mesh

SkoveNet agents discover each other automatically on local networks via mDNS. For wide-area deployments, you may need to manually connect nodes.

### Local Mesh Verification
Run two agents in separate terminals. You should see connection logs:
```text
[*] Peer Connected: 12D3KooW...
```

## The Controller Operations

To interact with the mesh, launch the **Controller** on your operator machine.

```bash
./bin/linux/controller
```

### 1. Authentication
All commands must be signed. Use your private key to authenticate your session:
```bash
> sign <your_private_key_base64>
```

### 2. Situational Awareness
List all discovered nodes in the mesh:
```bash
> peers
```

### 3. Targeting
Select a specific agent to interact with:
```bash
> use <peerID>
```

### 4. Direct Interaction
Once "attached" to an agent, you can run built-in commands or shell commands:
```bash
[peerID]> info      # Get system information
[peerID]> ls        # List directory
[peerID]> whoami    # Run shell command
```

### 5. Transitioning
To return to the global view or target another agent:
```bash
[peerID]> background
```

## Red Team Tip: Resilience
The mesh is self-healing. If you lose connection to your entry-point node, the controller will attempt to route commands through other available neighbors if they exist in your local peer list.
