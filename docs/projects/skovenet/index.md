# SkoveNet

**SkoveNet** is a decentralized Command & Control (C2) framework engineered to eliminate Single Points of Failure and ensure maximum operator anonymity.

Unlike client-server C2 models, SkoveNet implements a decoupled Agent-Controller architecture. This allows the operator to interface with the network through any active node, removing the dependency on a static command center and obfuscating the operator's physical location.

<p align="center">
  <img src="/skoving/assets/images/structure_diagram.png" alt="structure diagram" />
  <br>
  <b>No single point of failure</b>
</p>



## Core Features
- Fully decentralized P2P mesh network
- Automatic self-healing topology
- Max 5 neighbors per agent → tiny traffic footprint
- Operator = whoever has the cryptographic secret key
- Commands signed with Ed25519 → no spoofing
- End-to-end encrypted (Noise protocol)
- GossipSub broadcast (fast & reliable)
- Single binary, zero dependencies – works on Windows, Linux, macOS, ARM
- NAT traversal & hole punching built-in
- **`sgen`** — standalone agent generator (no Go toolchain required)

## Tutorials

Learn how to deploy and operate SkoveNet:

*   **[Getting Started](tutorials/getting-started.md)**: Environment setup and installation.
*   **[Agent Generation](tutorials/agent-generation.md)**: Creating implants with `sgen`.
*   **[Mission Execution](tutorials/mission-execution.md)**: Red team operations and mesh control.

## Reference Documentation

Deep dives into the system internals:

*   **[Architecture](reference/architecture/overview.md)**: How the decentralized mesh works.
*   **[Security Model](reference/architecture/security.md)**: Cryptography and authentication.
*   **[Command Reference](reference/features/commands.md)**: Built-in agent capabilities.
*   **[Developer Guide](reference/development/adding-commands.md)**: Extending SkoveNet with new commands.
*   **[Roadmap](reference/roadmap.md)**: Future features and planned enhancements.
