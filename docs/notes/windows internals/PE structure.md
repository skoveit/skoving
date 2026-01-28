The standard data structure for Windows executables (`.exe`, `.dll`, `.sys`).

1. **DOS Header** (`IMAGE_DOS_HEADER`)
	1. starts at 0x00 and its magic is `MZ`
	2. Field **`e_lfanew`** points to where the **NT Headers** start (the PE header location).
	
2. **DOS Stub** (legacy message like “This program cannot be run in DOS mode”)
3. **NT Headers** (`IMAGE_NT_HEADERS`)
    - **Signature**: `"PE\0\0"`
    - **File Header** (COFF header) `IMAGE_FILE_HEADER`
	    - **Machine** (x86 or x64)
	    - **NumberOfSections**
	    - **Characteristics** (flags: DLL, executable, etc.)
    - **Optional Header** (important for executables)
	    - **Magic**: PE32 vs PE32+ (32-bit vs 64-bit)
		- **AddressOfEntryPoint** (RVA of where execution starts)
		- **ImageBase** (preferred load base)
		- **SectionAlignment** (memory alignment) / **FileAlignment** (on-disk alignment)
		- **SizeOfImage**, **SizeOfHeaders**
		- **Subsystem** (GUI / Console)
		- **DataDirectories** (pointers to important tables)
			- An array of 16 structures pointing to important tables.
			- **Import Table:** Lists functions the file needs from other DLLs.
			- **Export Table:** Functions the file provides to others (common in DLLs).
			- **Resource Table**
			- **Base Relocation Table**
			- **TLS Table**
			- **Debug Directory**
4. **Section Table** (array of section headers)
	1. Each section has an `IMAGE_SECTION_HEADER` entry:
		- **Name** (e.g., `.text`, `.data`)
		- **VirtualAddress (RVA)** (where it maps in memory)
		- **VirtualSize**
		- **PointerToRawData** (file offset)
		- **SizeOfRawData**
		- **Characteristics** (R/W/X permissions)
5. **Sections**
	- **`.text`**: RX by default, which is great.
	- **`.data`**: initialized writable globals (often **RW-**)
	- **`.rdata`**: read-only constants/strings (often **R--**)
	- **`.rsrc`**: resources (icons, dialogs, version info) so it good with big payload. 
	- ... 


Store payload as a resource blob inside `.rsrc`, retrieve it at runtime as (address + size), and copy it to writable memory if you need to modify it.  
To create the `.rsrc` section directly (without IDE tools), define the payload in a resource script (`.rc`), compile it into a resource object (`.res` / `.o`), then link it with the main object during PE compilation.
