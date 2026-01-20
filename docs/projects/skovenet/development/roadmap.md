# Roadmap

Our vision is to make SkoveNet the most resilient C2 framework in existence.

## Phase 1: MVP (Completed)
- [x] Functional P2P mesh (libp2p).
- [x] Basic command propagation and execution.
- [x] mDNS local discovery.
- [x] Multi-platform support (Windows/Linux).
- [x] Basic interactive controller.

## Phase 2: Security & Resilience (In Progress)
- [ ] **Command Signing**: Enforce Ed25519 signatures on all critical operations.
- [ ] **GossipSub**: Migrate from flood routing to GossipSub for scalability.
- [ ] **Traffic Obfuscation**: Hide P2P patterns to evade NIDS.
- [ ] **Key Rotation**: Mechanism to revoke and rotate operator keys.

## Phase 3: Advanced Features (Planned)
- [ ] **File Transfer**: P2P file uploads/downloads.
- [ ] **Reverse Shell**: Streaming interactive shells over the mesh.
- [ ] **Module System**: Loadable plugins for agents.
- [ ] **Web UI**: Fully featured React/Vue dashboard for the controller.
- [ ] **Mobile Support**: potentially running agents on Android/iOS.
