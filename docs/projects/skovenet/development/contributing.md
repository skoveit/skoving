# Contributing to SkoveNet

We welcome contributions! SkoveNet is written in Go and aims for high code quality and minimal dependencies.

## Project Structure

```text
skovenet/
├── cmd/            # Entry points (main.go)
│   ├── agent/      # The agent binary
│   └── controller/ # The operator CLI
├── pkg/            # Library code
│   ├── node/       # P2P node logic & peer management
│   ├── protocol/   # Message definitions
│   ├── discovery/  # mDNS / DHT logic
│   └── command/    # Interaction handlers
├── static/         # Web assets (graph UI)
└── bin/            # Compiled binaries
```

## Development Guidelines

1.  **Language**: Go 1.25+
2.  **Formatting**: Always run `go fmt ./...` before committing.
3.  **Linting**: Use `golangci-lint` to check for issues.
4.  **Concurrency**: Use `sync.Mutex` or channels for thread safety. This is critical in P2P code.
5.  **Dependencies**: Keep external dependencies to a minimum. Use the standard library where possible.

## Testing

*   **Unit Tests**: Run `go test ./...`
*   **Local Mesh**: Manually run 2-3 agents in separate terminals to verify connectivity and command propagation.
