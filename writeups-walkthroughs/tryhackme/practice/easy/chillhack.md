# [ROOM_NAME]

![tryhackme.com - © TryHackMe](.gitbook/assets/tryhackme-logo-small.png)

> 🔬🌐 [ROOM_NAME](ROOM_LINK_HERE)
>
> *Description*
>
> ROOM_PICTURE_HERE

🎯 Target IP: 

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

