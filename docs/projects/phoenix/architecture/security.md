# Security Architecture

Security is paramount in PHOENIX. This document outlines the security model, cryptographic primitives, and threat mitigations.

## Cryptographic Identity

Every node in the network is identified by an **Ed25519** public key.
*   **Key Generation**: Upon first startup, a node generates a random Ed25519 keypair.
*   **Peer ID**: The Peer ID is derived from the public key, ensuring that a node cannot spoof another's ID without the private key.

## Transport Encryption

All P2P communications are encrypted in transit using the **Noise Protocol Framework**.
*   **Protocol**: `Nodes` use the `Noise` handshake (often `XX` pattern) to establish a secure, authenticated channel.
*   **Properties**:
    *   **Confidentiality**: No eavesdropper can read the command traffic.
    *   **Integrity**: Messages cannot be tampered with in transit.
    *   **Authenticity**: You are guaranteed to be talking to the Peer ID you expect.

## Command Signing & Authorization (Operator Mode)

To prevent unauthorized users from issuing commands to the mesh, PHOENIX implements an **Operator** model.

*   **Operator Key**: A special, secret Ed25519 key held *only* by the operator.
*   **Signing**: When the operator issues a command via the `sign` feature in the controller, the command payload is cryptographically signed.
*   **Verification**: Receiving nodes verify the signature against the hardcoded/distributed Operator Public Key. If the signature is invalid, the command is dropped.

### Using the Sign Command

```bash
# Sign in with a raw private key string
sign <private_key_string>

# Or load from a file
sign -path /path/to/secret.key
```

## Threat Model

| Threat | Mitigation |
| :--- | :--- |
| **Eavesdropping** | End-to-end encryption (Noise Protocol). |
| **Spoofing (Sybil)** | Ed25519 identities make it impossible to spoof specific IDs. |
| **Command Injection** | Unsigned commands are rejected by agents (when strictly configured). |
| **Replay Attacks** | Messages include a timestamp/nonce to prevent replaying old commands. |
| **Traffic Analysis** | (Future) Pluggable transports (obfuscated traffic) to look like HTTPS/DNS. |
