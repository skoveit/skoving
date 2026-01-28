
**PEB**: _Process Environment Block_ refers to the **structure in memory** that holds information about a specific user-mode process. see it with command > ___!peb___

**TCB**: _Thread Control Block_ is a field inside `ETHREAD` called `Tcb`, and it is of type `KTHREAD`.
**PCB**: _Process Control Block_ is a theoretical term referring to all the kernel-level information of a process. In Windows, it maps to the entire `EPROCESS` structure.


\_EPROCESS
	|\_KPROCES
	|ThreadListHead
		| \_ETHREAD 
		| \_ETHREAD
			|\_KTHREAD


![](https://user-images.githubusercontent.com/59355783/229268611-066a1413-f9e3-47eb-bacd-fe77e2762fcd.png )


### PPL 
 **PPL**: A kernel security feature that blocks memory reads/injection into sensitive processes like lsass.exe. Even admin-level tools can't access PPL processes without a bypass.

```windbg
!process 0 0 [name]
dt nt!\_EPROCESS [addr] Protection
```

to see if the lsass.exe is PPL: 
```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa"
```

Only specific processes are marked as PPL at launch. 
to turn it off or on from spesfic process:
```powershell
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v RunAsPPL /t REG_DWORD /d 0 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v RunAsPPL /t REG_DWORD /d 0 /f
```

🔹PPL Runtime bypass: [PPLKiller](https://github.com/Leo4j/PPLKiller)

---
### createprocess flow
`CreateProcessInternalW` in `kernel32.dll` is called first. It validates arguments like `lpApplicationName`, `lpCommandLine`, and `dwCreationFlags` (e.g., `CREATE_SUSPENDED`, `CREATE_NEW_CONSOLE`). It resolves the EXE path (adds `.exe` if missing) using `BasepSearchPath`, and prepares internal structs like `STARTUPINFO` and `UNICODE_STRING`. _(stage 1)_

It opens the file with `NtOpenFile`, reads the PE headers, and checks `IMAGE_OPTIONAL_HEADER.Subsystem` to decide if it's a GUI or console app. It also runs AppCompat logic (shims) if needed. _(stage 2)_

Next, it calls `RtlCreateUserProcess` in `ntdll.dll`, which wraps the syscall `NtCreateUserProcess`. That hands execution into kernel mode. _(stage 3)_

Inside the kernel, `NtCreateUserProcess` calls `PspAllocateProcess` to create the `EPROCESS` and `PspAllocateThread` for the initial `ETHREAD`. It maps the executable image using `MmCreateSection` and `MmMapViewOfSection`. _(stage 4)_

The system sets up the user-mode address space: base image, stack, TEB, and PEB. The entry point is set to `LdrInitializeThunk`, which will run user-mode loader logic later. _(stage 4 continued)_

If the process is a GUI app, it connects to `csrss.exe` using `CsrClientConnectToServer`, and initializes the LPC channel. If it's a console app, a console is created or inherited using `BasepCreateConsole`. _(stage 5)_

The kernel finishes setting up the thread: `KeInitThread` initializes `KTHREAD`, and `PspInsertThread` links it to the scheduler. Thread context is set to start at `RtlUserThreadStart`. _(stage 6)_

If not created suspended, `NtResumeThread` is called, and execution starts at the entry point defined in the PE header. The loader runs, main() starts, and the parent process is notified. _(stage 7)_