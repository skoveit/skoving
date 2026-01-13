# Troubleshooting

Common issues and solutions for PHOENIX operators.

## Connectivity Issues

### "No peers connected"
*   **Cause 1**: You are running a single node. Start at least one more.
*   **Cause 2**: Firewalls blocking the port. PHOENIX listens on a random high port by default.
    *   **Fix**: Check `iptables` or Windows Firewall.
*   **Cause 3**: Different networks. mDNS only works on local LAN.
    *   **Fix**: Use a Bootstrap node or ensure multicast is allowed between subnets.

### "Command timed out"
*   **Cause**: The target node is unreachable or the mesh is partitioned.
*   **Fix**: Check `radar` to see if the node is visible. Wait for the mesh to heal (reconnect).

## Build Errors

### "undefined reference" or "go.mod" errors
*   **Fix**: Run `go mod tidy` to ensure all dependencies are downloaded.
*   **Fix**: Ensure you have Go 1.25.4+.

### "Access Denied" on Windows
*   **Cause**: Anti-Virus might be flagging the binary.
*   **Fix**: Add an exclusion for the build directory or the binary.

## Logging

To debug, run the agent in a terminal and watch the standard output.
*   Look for `Host created...` logs.
*   Look for `[+] Peer connected` events.

If the binary exits immediately, check for permission errors or port conflicts.
