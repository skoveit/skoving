# Peer Discovery

Discovery is the process by which SkoveNet nodes find each other without a central server.

## 1. mDNS (Local Network)

**Multicast DNS (mDNS)** is enabled by default. It allows nodes on the same Local Area Network (LAN) to discover each other automatically.

*   **Service Name**: `_mesh-c2._tcp`
*   **Mechanism**: Nodes broadcast their presence. When a peer is detected, the node attempts to connect if it hasn't reached its max peer limit.
*   **Use Case**: rapid local deployment, testing, and lateral movement within a subnet.

## 2. Bootstrap Nodes (Internet)

For internet-wide connectivity where multicast is not available, SkoveNet uses **Bootstrap Nodes**.
*   These are stable nodes with known addresses (IP:Port).
*   New nodes connect to bootstrap nodes to join the mesh and find other peers.

## 3. DHT (Planned)

We are implementing a **Kademlia Distributed Hash Table (DHT)**.
*   **Function**: Allows nodes to "announce" themselves to the global network request peers closest to a random key.
*   **Benefit**: Enables fully decentralized peer discovery across the public internet without relying on hardcoded bootstrap lists.
