

1. Seatbelt
	-group=system,user,misc or -group=all 
	InternetSettings
2. SharpUp (privesc)  
        SharpUp.exe audit
            -> Runs all vulnerability checks regardless of integrity level or group membership.
        SharpUp.exe HijackablePaths
            -> Check only if there are modifiable paths in the user's %PATH% variable.
        SharpUp.exe audit HijackablePaths
            -> Check only for modifiable paths in the user's %PATH% regardless of integrity level or group membership.  

### Defenses
```powershell
# windows Defender
Get-MpComputerStatus | Select-Object -Property AntivirusEnabled, AMServiceEnabled, RealTimeProtectionEnabled

# App locker
reg query "HKLM\Software\Policies\Microsoft\Windows\SrpV2\"

(Get-AppLockerPolicy -Effective).RuleCollections | Select-Object RuleCollectionType, EnforcementMode

(Get-AppLockerPolicy -Effective).RuleCollections.Exe.Rules
```
 Try to bypass by using ["LOLBAS's"](https://lolbas-project.github.io/).

### Services

```powershell
# process
tasklist

# open ports
Get-NetTCPConnection -State Listen | Select-Object LocalAddress, LocalPort, OwningProcess, State | Sort-Object LocalPort


```