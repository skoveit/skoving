
**page_table** is an array for each process that convert from Virtual Memory to physical Memory. windows will fill this table with values starts with 0.

when you call `CreateFileMapping()` windows will add a new node in [[skove/notes/windows arch/windows internals notes/memory/memory mapping#Virtual Address Descriptor|VAD]] that contains starting[[skove/notes/windows arch/windows internals notes/memory/memory mapping#Virtual Page Number|VPN]], endingVPN and the filepath if this is points to file on disk.

When CPU try to convert Virtual Memory and find the value starts with 0 (means didn't used yet) it send interupt `page fault`. 

Windows will execute `KiPageFault()` that take this virtual address and seach in the VAD so if that inside the range of our node will ask the disk driver to copy the `node.filepath` to the physical address, then windows will write this physical address inside the page_table in the virtual address index. 

windows will let the process to continue processing.


>CreateFileMapping() doesn't allocate physical RAM immediately. It creates a Section Object in kernel space and adds a node to the process's VAD Tree.

>If hFile = INVALID_HANDLE_VALUE, the VAD node points to the Paging File instead of a specific disk file.

## Soft Link 
you can create mapping but this time from RAM to RAM like soft link. it good when multiple process use the same DLL. also for sharing memory between process.
just use `CreateFileMapping()` with `INVALID_HANDLE_VALUE`.  

### Virtual Page Number 
virtual address and physical address point to only one Byte, but page points to 4096 Byte in one page so to find a address you need to get the page then inside this page move another steps to reach your value, we call that steps **offset** 

### Virtual Address Descriptor
it is a binary tree and the database of the memory in kernal.   

