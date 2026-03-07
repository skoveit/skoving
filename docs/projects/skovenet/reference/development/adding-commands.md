# Adding New Agent Commands

The SkoveNet C2 agent features a modular command execution system. New commands can be added easily by implementing the `Command` interface and registering them with the agent's `Executor`. This allows you to add specific built-in functionality (e.g., `upload`, `download`, `screenshot`) without cluttering the existing code.

## 1. Implement the `Command` Interface

To add a new command, create a new file in `pkg/command/` (e.g., `pkg/command/download.go`) and define a struct that implements the `Command` interface:

```go
package command

import (
	"context"
	"fmt"
)

type DownloadCommand struct{}

func NewDownloadCommand() *DownloadCommand {
	return &DownloadCommand{}
}

// Name returns the trigger word for this command.
// When the controller sends a command starting with this word, this struct handles it.
func (c *DownloadCommand) Name() string {
	return "download"
}

// Description provides a brief summary of what the command does.
func (c *DownloadCommand) Description() string {
	return "Downloads a file from the C2 server"
}

// Execute performs the command logic.
// It receives the context and the raw arguments string (everything after the command name).
// It is up to the command to parse `rawArgs` contextually (e.g., split by space, parse paths).
func (c *DownloadCommand) Execute(ctx context.Context, rawArgs string) (string, error) {
	if rawArgs == "" {
		return "", fmt.Errorf("missing file path")
	}
	
	// ... logic to read/download the file ...
	
	return fmt.Sprintf("Successfully downloaded %s", rawArgs), nil
}
```

## 2. Register the Command

Once you have implemented your command struct, register it with the executor when the `Handler` is created.

Open `pkg/command/handler.go` and locate the `NewHandler` function. Add your newly created command using `executor.Register()`:

```go
func NewHandler(n *node.Node) *Handler {
	executor := NewExecutor()
	
	// Register custom commands here
	executor.Register(NewDownloadCommand())
	// executor.Register(NewUploadCommand())
	
	return &Handler{
		node:     n,
		executor: executor,
	}
}
```

## How It Works (Shell Fallback)

Whenever a command is received from the controller:
1. The executor parses the first word (the command name).
2. It checks if the command name matches against any registered `Command` explicitly.
3. If a match is found, it forwards the rest of the arguments to that `Execute` method.
4. **Fallback:** If no match is found, the **entire command string** seamlessly falls back to the native `shell` executor (`shell_unix.go` or `shell_windows.go`).

This backward-compatible approach ensures that standard terminal commands like `ls -la`, `whoami`, or `cat file.txt` continue to work natively without requiring you to explicitly register those commands.
