# RoomName

![tryhackme.com - © TryHackMe](.gitbook/assets/tryhackme-logo-small.png)

## Intro

| Room Info           | RoomLogo.png               |
| :------------------ | -------------------------- |
| 🔗 Name              | RoomName&Link              |
| 🎯 Target IP         | `IP`                       |
| 📈 Difficulty level  | 🟢Easy 🟡Medium 🔴Hard/Insane |
| 💲 Subscription type | Free/Sub only              |
| 🪟 🐧OS               | Linux/Windows              |

---

## Recon

```bash
su
echo "IP roomname.thm" >> /etc/hosts

# At the end of the room
# To clean up the last line from the /etc/hosts file
sed -i '$ d' /etc/hosts
```

Start Reconnaissance

```bash
mkdir $HOME/thm/roomname
cd $HOME/thm/roomname
nmap roomname.thm

nmap -sV -sC -Pn -oA roomname roomname.thm
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

