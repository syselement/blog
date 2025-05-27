# box_NAME

![hackthebox.com - © HACKTHEBOX](.gitbook/assets/logo-htb2.png)

---

## Intro

| Box Info            | BoxLogo.png                 |
| :------------------ | --------------------------- |
| 🔗 Name              | BoxName&Link                |
| 🎯 Target IP         | `IP`                        |
| 📈 Difficulty level  | 🟩Easy 🟨Medium 🟥Hard ⬛Insane |
| 💲 Subscription type | Free/Sub only               |
| 🪟 🐧OS               | Linux/Windows               |

---

## Recon

```bash
su
echo "IP boxname.htb" >> /etc/hosts

# At the end of the box
# To clean up the last line from the /etc/hosts file
sed -i '$ d' /etc/hosts
```

Start Reconnaissance

```bash
mkdir $HOME/htb/boxname
cd $HOME/htb/boxname
nmap boxname.htb

nmap -sV -sC -Pn -oA boxname boxname.htb
```

```bash
# Nmap Output

```



---

## Exploitation

- 

```bash

```



---

## Privilege Escalation

- 

```bash

```



### Get Flags

- 

```bash
find / -type f -name 'flag.txt' 2>/dev/null 

find / -type f -iname user.txt 2>/dev/null

find / -type f -iname root.txt 2>/dev/null
```



---

## Post Exploitation

- 

```bash

```



---

## Extra

------

