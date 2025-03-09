# Rocky Linux

![](.gitbook/assets/rocky.png)

---

## 🌐 Resources 🔗

> - [Rocky Linux](https://rockylinux.org/)
> - [Rocky Linux Instructional Books - Documentation](https://docs.rockylinux.org/books/)

### 📚 [The UNIX/Linux Philosophy](https://docs.rockylinux.org/books/admin_guide/01-presentation/#the-unixlinux-philosophy)

- Treat everything as a file.
- Value portability.
- Do one thing and do it well.
- **KISS**: Keep It Simple Stupid.
- "UNIX is a simple operating system, but you have to be a genius to understand the simplicity." (Dennis Ritchie)
- "Unix is user-friendly. It just isn't promiscuous about which users it's friendly with." (Steven King)

---

## Commands

```bash
# command [option(s)] [argument(s)]

/bin/bash
cat /etc/passwd
cat /etc/shadow
cat /etc/profile
cat /etc/sudoers

apropos --exact password -a change
whatis clear
man clear

reboot
shutdown -r +5
shutdown now
shutdown -h 0:30
shutdown -h 0:30 "Server shutdown at 0:30"
shutdown --show
shutdown -c

history
clear
echo -ne "\t Hello \n"
echo $LANG
echo $PATH
date
date +%F
date +%x
date +%T
date -d 20250310 +%j

id root
whoami
who
who -r

pwd
...
```



---

## Tools

```bash

```

