# Agent Generation

The **sgen** utility is the Swiss Army knife of SkoveNet, handling everything from identity creation to implant generation. It embeds a full Go toolchain and the agent source code, allowing you to generate implants on the fly.

## Identity Management (`keygen`)

Before generating an agent, you need an operator identity. The `keygen` command generates an Ed25519 keypair used to sign and authenticate your commands within the mesh. Without this, you cannot issue commands to agents.

```bash
./sgen keygen
```
*Save your private key securely; you will need it to sign into the controller.*

## Discovering Templates (`list`)

To see what platforms and architectures are currently supported by your `sgen` binary, use the `list` command. This ensures you know exactly what options are available for your current engagement.

```bash
./sgen list
```

## Generating Agents (`generate`)

Generating an agent is as simple as picking a target from the list.

### Cross-Compilation Example
```bash
# Generate a Windows Agent
./sgen generate --os windows --arch amd64 --output finance_audit.exe
```

## Configuration Flags

| Flag | Description |
| :--- | :--- |
| `--os` | Target operating system (linux, windows, darwin) |
| `--arch` | Target architecture (amd64, arm64) |
| `--key` | Embed a specific public key for authentication |
| `--output` | Custom filename for the generated binary |

Once your identity is created and your agent is deployed, proceed to [Mission Execution](mission-execution.md).
