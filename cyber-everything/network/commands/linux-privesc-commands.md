# Linux Privesc Commands

![](../../../.gitbook/assets/root.png)

***

## 🌐 Resources 🔗

> * [Ignitetechnologies/Privilege-Escalation](https://github.com/Ignitetechnologies/Privilege-Escalation)
> * [Linux - Privilege Escalation - Internal All The Things](https://swisskyrepo.github.io/InternalAllTheThings/redteam/escalation/linux-privilege-escalation/)
> * [PEASS-ng/linPEAS](https://github.com/peass-ng/PEASS-ng/tree/master/linPEAS)
> * [Basic Linux Privilege Escalation - g0tmi1k](https://blog.g0tmi1k.com/2011/08/basic-linux-privilege-escalation/)
> * [Linux Privilege Escalation – Resources – Siren Security](https://sirensecurity.io/blog/linux-privilege-escalation-resources/)
> * [unix-privesc-check | pentestmonkey](https://pentestmonkey.net/tools/audit/unix-privesc-check)
> * [TryHackMe | Linux Privilege Escalation](https://tryhackme.com/room/linprivesc)
> * [Privilege Escalation on Linux (With Examples)](https://delinea.com/blog/linux-privilege-escalation)
>
> **Courses**
>
> * [Linux Privilege Escalation for OSCP & Beyond! | Tib3rius (One-Time Purchase)](https://courses.tib3rius.com/p/linux-privilege-escalation-for-oscp-beyond)
> * [Linux Privilege Escalation | TCM Security, Inc.](https://academy.tcm-sec.com/p/linux-privilege-escalation)

***

```bash
# https://www.exploit-db.com/exploits/1518
cd /home/user/tools/mysql-udf
gcc -g -c raptor_udf2.c -fPIC
gcc -g -shared -Wl,-soname,raptor_udf2.so -o raptor_udf2.so raptor_udf2.o -lc
```

```bash
ls -l /etc/shadow
# Bruteforce hashes or if world-writable, generate new password and replace root pw
mkpasswd -m sha-512 pwhere

ls -l /etc/passwd
# If world-writable, generate new password and replace root pw
openssl passwd pwhere
```

```bash
sudo -l
# https://gtfobins.github.io/

cat /etc/crontab
# check file/scripts permissions, path environment variable
```

```bash
find / -user root -perm -4000 -exec ls -ldb {} \; 2>/dev/null
find / -type f -a \( -perm -u+s -o -perm -g+s \) -exec ls -l {} \; 2> /dev/null
find / -type f -perm -4000 2>/dev/null

# https://www.exploit-db.com/
```

...

***
