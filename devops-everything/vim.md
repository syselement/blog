# [Vim](https://www.vim.org/)

![vim.org](.gitbook/assets/vim.png)

---

## 🌐 Resources 🔗

> - [VIM Docs](https://www.vim.org/docs.php)
> - [Vim Cheat Sheet](https://vim.rtorr.com/)

---

## Commands

```bash
sudo apt install vim
```

```bash
vim <filename>
```

```bash
# "i" key - enters insert mode
# "ESC" key - switch to command mode

# In command mode
:wq # save and quit
:q!	# force quit without save
dd	# delete entire line
d5	# delete next 5 lines
u 	# undo

A	# jump to end of line and switch to insert mode
0	# jump to start of the line
$	# jump to end of the line
10G # jump to line 10

/<string> # search for string
	n # jump to next match
	N # search in opposite direction

:%s/old/new # replace 'old' with 'new' throughout the file
```

---

