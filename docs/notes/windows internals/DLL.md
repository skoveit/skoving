
Windows often loads specific DLLs at the same virtual address across all processes to optimize memory and performance. 

>  [memory mapping](https://skoveit.github.io/skoving/notes/windows%20internals/memory/memory%20mapping/)

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

### PE Without an Entry Point
**DLL (Dynamic Link Library)** is a **Portable Executable (PE)** file, just like an `.exe`. However, there is a fundamental architectural difference:

- **No Main Execution:** A DLL is "passive." It lacks a standard `main()` or `WinMain()` because it isn't designed to run as a standalone process.
- **DllMain:** Instead of an entry point that starts the program, it has an optional `DllMain` function. This acts as a "signal handler" that the OS calls only when a process or thread attaches/detaches from the library.
- **The Flag:** In the PE Header (`IMAGE_FILE_HEADER`), a specific bit (**IMAGE_FILE_DLL**) is set to tell Windows, "This is a library, not an application."

---
### 2. Loading vs. Mapping

Windows does **not** "load" a fresh copy of a DLL into the RAM of every process. If 100 programs use `kernel32.dll`, Windows doesn't waste RAM by loading it 100 times.
- **Shared Physical Memory:** The OS loads the DLL into physical memory **once**.
- **Virtual Mapping:** Using the **Memory Manager**, the OS simply "maps" that same physical memory into the **Virtual Address Space** of each process.
- **The Result:** Every process _thinks_ it has its own copy of the DLL at a specific address, but they are all looking at the same physical silicon chips in your RAM.

---
### 3. Modification & The "Copy-on-Write" (CoW) Mechanism
This brings up a critical question: _If I hook a function or modify a Syscall (Instruction patching) in one process, does it break the DLL for everyone else?_ **The answer is No, thanks to Copy-on-Write:**

1. **Read-Only Sharing:** Initially, the memory pages of the DLL are marked as shared and read-only.
    
2. **The Trigger:** When your process attempts to write/modify a byte in that DLL (e.g., changing a `syscall` instruction to a `jmp`), a **Page Fault** occurs.
    
3. **The Fork:** The Windows Kernel intercepts this fault. It realizes you want a custom version. It then:
    - Copies that specific memory page into a **new physical location**.
    - Remaps your process's virtual address to this **new private copy**.
    - Allows the modification on your private copy only.
        
4. **Isolation:** All other processes continue using the original, clean version of the DLL.
    

---

### 4. Anatomy of a DLL (The Structure)

A DLL follows the standard PE structure but focuses heavily on the **Data Directories**:

|**Section**|**Purpose**|
|---|---|
|**DOS Header**|The "MZ" signature; legacy stub for compatibility.|
|**PE Header**|Contains metadata (Machine type, Number of sections, Timestamp).|
|**Export Directory**|**The heart of the DLL.** Lists the names and addresses of functions it provides to others.|
|**Import Directory**|Lists other DLLs _this_ DLL needs to function (Dependencies).|
|**.text Section**|Contains the actual executable machine code (the logic).|
|**.data Section**|Contains global/static variables.|
|**.reloc Section**|Crucial for DLLs. If the DLL can't be mapped at its "Preferred Base Address," this table tells the OS how to fix the addresses.|

### Summary

> "A DLL is a shared blueprint. Windows maps the same physical code to everyone to save space. However, the moment you try to 'vandalize' or hook that code for your specific process, Windows silently gives you your own private 'paper' to write on, ensuring the rest of the system remains untouched."
