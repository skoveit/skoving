# Networking & Protocol

SkoveNet leverages the **libp2p** stack to provide a robust, modular networking layer.

## The Transport Layer

The system supports multiple transports to ensure connectivity in various environments:

*   **TCP**: Standard reliable transport.
*   **WebSockets**: Useful for bypassing some firewalls that allow HTTP/S traffic.
*   **WebRTC** (Planned/Partial): For direct browser-to-node communication and superior NAT hole punching.

## Encryption & Security

All connections are automatically encrypted using the **Noise Protocol Framework**.
*   **Handshake**: Nodes perform a handshake to establish a shared secret.
*   **Authentication**: Peer IDs are cryptographically derived from their public keys, preventing identity spoofing.

## Routing Logic

How does a command from Node A reach specific Node Z if they aren't directly connected?

### Flood Routing (Current)
Currently, SkoveNet uses a controlled flood (gossip) mechanism for message delivery.

1.  **Origin**: Node A creates a message with `Target=Z` and `TTL=10`.
2.  **Propagation**: A sends it to all connected peers (B, C).
3.  **Forwarding**: B receives it.
    *   Checks if it has seen this Message ID before (deduplication).
    *   Decrements TTL.
    *   Forwards to its peers (D, E).
4.  **Delivery**: Eventually, the message reaches Z, or the TTL expires.

### GossipSub (Planned)
For larger scale, we plan to migrate to **GossipSub**, a more efficient pubsub router that uses mesh maintenance strategies to reduce bandwidth usage while ensuring high delivery reliability.

## NAT Traversal

One of the biggest challenges in P2P is connectivity behind NATs (Network Address Translation). SkoveNet uses:

1.  **UPnP / NAT-PMP**: Attempts to automatically map ports on the router.
2.  **AutoNAT**: Nodes ask peers "What is my public IP?" to determine reachability.
3.  **Hole Punching**: Techniques to open direct lines between two nodes behind NATs without a relay.
