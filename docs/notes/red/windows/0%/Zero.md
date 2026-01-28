# Domain Enum
```bash
ldapsearch -H ldap://10.10.239.149 -x -LLL -s base -b ""
```
# Usernames Enum 
### 1. Unauthenticated / Null Session Enumeration
```bash
# bruteforce 
kerbrute userenum --dc 10.10.239.149 -d vulnnet-rst.local users.txt
impacket-lookupsid vulnnet-rst.local/guest@10.10.72.218 # 
nmap --script krb5-enum-users --script-args krb5-enum-users.realm='corp.local',userdb=users.txt -p 88 10.10.10.10
nxc smb <dc_ip> --rid-brute 10000 # bruteforcing RID

# enum4linux
enum4linux -U <DC_IP>

# SMB Null Session
nxc smb <dc_ip> --users
smbclient -L //<DC_IP> -N
net rpc share -S <DC_IP> -N

# LDAP anonymous Bind 
ldapsearch -x -H ldap:// -b "DC=lab,DC=local" "(objectClass=user)" 

# RPC Null Session
rpcclient -U "" -N <DC_IP>
rpcclient $> enumdomusers
rpcclient $> queryuser <RID>
```

### 2. Authenticated
- powershell AD Module
- powerview 
- cmd net user /domain
- LDAP Queries -> [[skove/skoving/notes/red/windows/0%/AD-enum#Enum]] 
- SharpHound


# Only username 
```bash
impacket-GetNPUsers <domain>/ -usersfile users.txt
```
