# Boxname

![hackthebox.com - © HACKTHEBOX](.gitbook/assets/logo-htb2.png)

---

## Intro

| Box Info           | BoxLogo.png-ResizeTo-70x70  |
| :----------------- | --------------------------- |
| 🔗 Name             | Boxname + Link              |
| 🎯 Target IP        | `BOX_IP`                    |
| 📈 Difficulty level | 🟩Easy 🟨Medium 🟥Hard ⬛Insane |
| 🪟 🐧OS              | Linux/Windows               |

---

## Recon

```bash
mkdir -p $HOME/htb/boxname/nmap
cd $HOME/htb/boxname
```

Start Reconnaissance

```bash
# Fast full TCP port scan
nmap -p- --min-rate 10000 BOX_IP
```

```bash
# Nmap Output
```

```bash
# Scan open ports with default scripts and version detection
nmap -p <PORTS> -sC -sV -vv -oA nmap/boxname BOX_IP
```

```bash
# Nmap Output
```

```bash
grep -i boxname nmap/boxname.nmap

# Subdomains discovery
# ffuf ...
```

Add the found values to the `/etc/hosts` file

```bash
# e.g. adeguate with found values, hostnames, ffuf
sudo sh -c 'echo "BOX_IP boxname.htb" >> /etc/hosts' && ping -c 3 boxname.htb

# At the end of the box
# To clean up the last line from the /etc/hosts file
sudo sed -i '$ d' /etc/hosts
```





---

## Exploitation

- 

```bash

```





---

## Foothold

### Shell as user NAME

- 

```bash
# User Flag
find / -type f -iname user.txt 2>/dev/null
```





---

## Lateral Movement

- 

```bash

```

### Shell as user NAME

- 

```bash
# User Flag
find / -type f -iname user.txt 2>/dev/null
```





---

## Privilege Escalation

- 

```bash

```



### Shell as root

- 

```bash
# Root Flag
find / -type f -name 'flag.txt' 2>/dev/null
find / -type f -iname root.txt 2>/dev/null
```





---

## Post Exploitation

- 

```bash

```



---

## Summary

- 



---

## Extra

- 



------

