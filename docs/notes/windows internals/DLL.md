
Windows often loads specific DLLs at the same virtual address across all processes to optimize memory and performance.

DLL Entry Point: (`DllMain`)
1. **DLL_PROCESS_ATTACH:** Triggered when a process loads the DLL.
2. **DLL_THREAD_ATTACH:** Triggered when a process creates a new thread.
3. **DLL_THREAD_DETACH:** Triggered when a thread exits normally.
4. **DLL_PROCESS_DETACH:** Triggered when a process unloads the DLL.

**Exporting:** To make a function available to other programs, use the `extern` and `__declspec(dllexport)` keywords.

**Dynamic Linking:** Loading and linking code at **runtime** rather than compile time. This process involves four main steps:

1. LoadLibrary() -> Loads the DLL into the process address space if not already present.
2. GetModuleHandle() -> Retrieves a handle to a DLL that is already loaded.
3. GetProcAddress() -> Retrieves the memory address of a specific exported function.
4. type-casting -> Converts the address into a function pointer to execute the code.


