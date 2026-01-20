# Welcome to SkoveNet

**SkoveNet** is a fully decentralized peer-to-peer (P2P) Command & Control (C2) system designed to eliminate the single point of failure inherent in traditional C2 architectures.

Unlike conventional systems that rely on a central server or specific domain, SkoveNet operates as a resilient, self-healing mesh network.

## The Core Philosophy

> **No server. No domain. No single point of failure.**

Traditional C2 systems work like a classic army: one central command relays orders to agents. If that command center (IP, domain, or server) is taken down, the entire network goes blind.

**SkoveNet** inverts this model. The "server" is abstract—it is simply whoever possesses the cryptographic secret key. Commands can be injected from *any* node in the network and will propagate to the intended target(s).

## Key Features

*   **Fully Decentralized**: A P2P mesh graph where every node is equal.
*   **Stealthy**: Max 5 neighbors per agent ensures a tiny network traffic footprint.
*   **Self-Healing**: The network automatically re-routes and recovers if nodes go offline.
*   **Secure**:
    *   **Cryptographic Identity**: All commands are signed with Ed25519 keys.
    *   **End-to-End Encryption**: All traffic is encrypted using the Noise protocol.
*   **Resilient**: Zero dependencies, single binary deployment for Windows, Linux, macOS, and ARM.

## Why SkoveNet?

| Feature | Traditional C2 | SkoveNet |
| :--- | :--- | :--- |
| **Central Server?** | Yes | **Never** |
| **Takedown Risk** | High (Block 1 IP) | **Impossible** (Must block all nodes) |
| **Operator Location** | Fixed / Proxy | **Anywhere** (Any node can be operator) |
| **Network Structure** | Hub-and-Spoke | **Mesh Graph** |
| **Traffic Pattern** | Beaconing to Server | **Low-volume P2P Gossip** |

## Getting Started

Ready to dive in?

*   Check out the [Installation Guide](getting-started/installation.md) to build SkoveNet.
*   Read the [Quick Start](getting-started/quickstart.md) to run your first mesh.
*   Explore the [Architecture](architecture/overview.md) to understand how it works under the hood.
