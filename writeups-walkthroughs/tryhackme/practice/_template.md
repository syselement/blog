# ROOM_NAME

![tryhackme.com - © TryHackMe](.gitbook/assets/tryhackme-logo-small.png)

## Room Info

> **Name**
>
> 🔗 ROOM_LINK_HERE
>
>  **Description**
>
> 📝 *DESCRIPTION_HERE*
>
> ROOM_PICTURE_HERE
>
> **Target IP**
>
> 🎯 `IP_HERE`

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
mkdir thm/roomname
cd thm/roomname
nmap roomname.thm

nmap -sV -sC -Pn -oA roomname roomname.thm
```

```bash

```



## Exploitation

- 

```bash

```





## Privilege Escalation

- 

```bash

```





## Post Exploitation

- 

```bash

```





## Flags

- 

```bash
find / -type f -name 'flag.txt' 2>/dev/null 

find / -type f -iname user.txt 2>/dev/null

find / -type f -iname root.txt 2>/dev/null
```





<details>
<summary>Reveal Flag - user.txt: 🚩</summary>



`[FLAG_HERE]`

[FLAG_SCREEN_HERE]

</details>





<details>
<summary>Reveal Flag - root.txt: 🚩</summary>



`[FLAG_HERE]`

[FLAG_SCREEN_HERE]

</details>







## Extra

------

