# Installation Guide

PHOENIX is built as a single, static binary with zero external dependencies. This makes deployment trivial across various operating systems.

## Prerequisites

*   **Go**: Version 1.25.4 or higher.
*   **Git**: To clone the repository.
*   **Make** (Optional): For easier build management if a Makefile exists.

## Building from Source

1.  **Clone the Repository**

    ```bash
    git clone https://github.com/skoveit/nostalgia.git
    cd nostalgia
    ```

2.  **Build the Project**

    We provide a `build.sh` script to automate the build process for multiple platforms.

    ```bash
    chmod +x build.sh
    ./build.sh
    ```

    This script will compile binaries for:
    *   Linux (amd64, arm64)
    *   Windows (amd64)
    *   macOS (Darwin arm64)

3.  **Verify the Binaries**

    Check the `bin/` directory for the output files:

    ```bash
    ls -l bin/
    # Should see:
    # nostalgia-linux-amd64
    # nostalgia-windows-amd64.exe
    # ...
    ```

## Manual Build

If you prefer to build manually for your specific architecture:

```bash
# For current OS/Arch
go build -o nostalgia cmd/agent/main.go

# For standard Linux
GOOS=linux GOARCH=amd64 go build -o nostalgia-linux cmd/agent/main.go
```

## Troubleshooting Build Issues

*   **Go Version**: Ensure you are running Go 1.25.4+. Check with `go version`.
*   **Dependencies**: Run `go mod tidy` to resolve missing dependencies if you encounter import errors.
