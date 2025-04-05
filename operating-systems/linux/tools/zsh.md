# Zsh & Oh-My-Zsh

---

## 🌐 Resources 🔗

> - [Zsh](https://www.zsh.org/)
>
>   - [zsh community projects](https://github.com/zsh-users)
>
> - [Oh My Zsh](https://github.com/ohmyzsh/ohmyzsh)
>
> - [Zsh for Humans](https://github.com/romkatv/zsh4humans)
>
> - [Nerd Fonts](https://www.nerdfonts.com/)
>
> - e.g. [NeuralNine/config-files](https://github.com/NeuralNine/config-files)
>

---

## Zsh Installation

```bash
sudo apt update -y && sudo apt install -y curl eza git-all zsh
sudo chsh -s $(which zsh) $(whoami)

zsh
```

- If `eza` does not install via `apt`, install it manually

```bash
sudo sh -c '
    apt update &&
    apt install -y gpg &&
    mkdir -p /usr/share/keyrings &&
    wget -qO- https://raw.githubusercontent.com/eza-community/eza/main/deb.asc | gpg --dearmor -o /usr/share/keyrings/gierens.gpg &&
    echo "deb [arch=amd64 signed-by=/usr/share/keyrings/gierens.gpg] http://deb.gierens.de stable main" | tee /etc/apt/sources.list.d/gierens.list &&
    chmod 644 /usr/share/keyrings/gierens.gpg /etc/apt/sources.list.d/gierens.list &&
    apt update &&
    apt install -y eza
'
```

- Install a specific [nerd font](https://www.nerdfonts.com/font-downloads) and set it as terminal emulator font
  - e.g. **JetBrainsMono Nerd Font**

```bash
cd
mkdir -p $HOME/.local/share/fonts
cd $HOME/.local/share/fonts
curl -fLO https://github.com/ryanoasis/nerd-fonts/releases/latest/download/JetBrainsMono.zip
unzip JetBrainsMono.zip
rm JetBrainsMono.zip

fc-cache -fv

# Set "JeBrainsMono Nerd Font Mono" Regular font for the terminal
# e.g. for Terminator
sed -i '/\[\[default\]\]/,/^\[/s/^ *font = .*/    font = JetBrainsMono Nerd Font Mono 16/' "$HOME/.config/terminator/config"
```

---

## [OhMyZsh](https://github.com/ohmyzsh/ohmyzsh)

```bash
sh -c "$(wget -O- https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

# Confirm with y to chage the default shell to zsh
```

Install the desired plugins and themes

```bash
# Auto-suggestions
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions

# Syntax highlighting
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting

# Powerlevek10k Theme
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/themes/powerlevel10k
```

### zshrc

Edit the `$HOME/.zshrc`

- Change the Zsh theme to use Powerlevel10k, add or update `plugins` to include additional Zsh plugins and add some lines before the `source .*oh-my-zsh.sh` command for additional configuration

```bash
ZSHRC="$HOME/.zshrc"

# Set the new theme
sed -i 's/^ZSH_THEME=.*/ZSH_THEME="powerlevel10k\/powerlevel10k"/' "$ZSHRC"

# Replace the plugins line with the new plugins
sed -i '/^plugins=/ { s/=.*/=(command-not-found kubectl zsh-autosuggestions zsh-syntax-highlighting)/; }' "$ZSHRC"

# Add additional lines before sourcing oh-my-zsh
sed -i '/^source .*oh-my-zsh.sh/ i \
# Fix errors\
ZSH_DISABLE_COMPFIX="true"\n\
\
# Skip all aliases in lib files\
zstyle '\'' :omz:lib:* '\'' aliases no\n' "$ZSHRC"
```

- Check the file and restart `zsh` to apply the changes and configure Powerlevel10k Theme.

```bash
zsh

# Add "user" to be shown in the prompt
grep -q '^[[:space:]]*user[[:space:]]*$' "$HOME/.p10k.zsh" || sed -i '/os_icon.*# os identifier/ a\    user' "$HOME/.p10k.zsh"
```

> **EXTRA**
>
> Set the same `ohmyzsh` config for the `root` user by symlinking the current user's `zsh` configuration.
>
> ```bash
> ROOTUSER=root
> USER_HOME_PATH=~$ROOTUSER
> 
> sudo mv ${USER_HOME_PATH}/.zshrc ${USER_HOME_PATH}/.zshrc.bak
> sudo ln -sf $HOME/.zshrc ${USER_HOME_PATH}/.zshrc
> sudo ln -sf $HOME/.oh-my-zsh ${USER_HOME_PATH}/.oh-my-zsh
> sudo ln -sf $HOME/.p10k.zsh ${USER_HOME_PATH}/.p10k.zsh
> sudo chsh -s $(which zsh) $ROOTUSER
> ```
>

### aliases

Define custom **Aliases** within the `ZSH_CUSTOM` folder (if a `oh-my-zsh` user), or in the `$HOME/.zshrc` file if only `zsh` user.

```bash
# e.g. of my custom aliases

sudo tee $ZSH_CUSTOM/aliases.zsh > /dev/null <<EOF
# Alias to update the system
alias updateos='sudo sh -c "sudo apt update && sudo apt -y upgrade && sudo apt -y autoremove"'

# Comment above & Uncomment the following for full Ubuntu + Snap + Brew update
# alias updateos='sudo sh -c "sudo apt update && sudo apt -y upgrade && sudo apt -y autoremove && sudo snap refresh" && brew upgrade'

# OpenVpn Aliases
alias htbvpn='sudo openvpn --config ~/htb/htb.ovpn --daemon'        # HTB FREE VPN
alias htbvipvpn='sudo openvpn --config ~/htb/htbvip.ovpn --daemon'  # HTB VIP VPN
alias thmvpn='sudo openvpn --config ~/thm/thm.ovpn --daemon'
alias pwnxvpn='sudo openvpn --config ~/pwnx/pwnx.ovpn --daemon'
alias killopenvpn='sudo pkill openvpn'

# Additional Aliases
alias clipcopy='xclip -selection clipboard'
alias df='df -h'
alias diff='diff --color=auto'
alias dir='dir --color=auto'
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'
alias grep='grep --color=auto'
alias h='history -100 -1'
alias history=omz_history
alias hl='history | less'
alias hs='history | grep'
alias hsi='history | grep -i'
alias hz=omz_history
alias ipa='ip -br -c a'
alias l='eza -lah --group-directories-first'
alias la='ls -A'
alias ll='l -T'
alias ls='ls -lh --color=auto'
alias mask='awk '"'"'{ printf substr($0, 1, 5); for (i=6; i<=length($0); i++) printf "*"; print "" }'"'"''
alias mobsf='docker run -it --rm --name mobsf -p 8000:8000 -v ~/docker/mobsf:/home/mobsf/.MobSF opensecurity/mobile-security-framework-mobsf:latest'
alias p3='python3'
alias ports='ss -lpntu'
alias python='python3'
alias sudo='sudo -v; sudo '
alias ugq='ugrep --pretty --hidden -Qria'
alias vdir='vdir --color=auto'
EOF
```

### path

Set Custom **PATH**.

```bash
# e.g. of my custom PATH

sudo tee $ZSH_CUSTOM/my_paths.zsh > /dev/null <<EOF
# Add go to path
export PATH=\$PATH:\$HOME/go/bin
EOF
```

Reboot the system to apply all the `zsh` settings

```bash
reboot
```

---

