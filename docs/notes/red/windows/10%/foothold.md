
#### username + password (or hash) 
- **SMB / Admin Shares** → `psexec.py`, `smbexec.py`, `wmiexec.py`, `PsExec.exe` PowerShell `Invoke-WmiMethod`
- **WinRM (PS Remoting)** → `evil-winrm`, `Enter-PSSession`, `Invoke-Command`
- **SSH** -> `ssh`
- **DCOM** → `Invoke-DCOM.ps1`, MMC20.Application, ShellWindows, `dcomexec.py`
- **Scheduled Tasks** → `schtasks.exe`, `at.exe`
- **SCM (Services)** → `sc.exe`, PsExec technique
- **MSSQL** → `mssqlclient.py`, `xp_cmdshell`
- **LDAP / Kerberos (Domain)** → `ldapsearch`, Impacket Kerberos tools
- **VPN / Web Portals (OWA, etc.)** → reuse creds for remote access
- **RDP** → `xfreerdp`, `rdesktop`, `mstsc`

