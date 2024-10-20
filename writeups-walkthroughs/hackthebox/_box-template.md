# box_NAME

![hackthebox.com - © HACKTHEBOX](.gitbook/assets/logo-htb2.png)

## box Info

> **Name**
>
> 🔗 box_LINK_HERE
>
>  box_PICTURE_HERE
>
> **Description**
>
> 📝 *DESCRIPTION_HERE*
>
> **Target IP**
>
> 🎯 `IP_HERE`

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





## Get Flags

- 

```bash
find / -type f -name 'flag.txt' 2>/dev/null 

find / -type f -iname user.txt 2>/dev/null

find / -type f -iname root.txt 2>/dev/null
```



## Extra

------

