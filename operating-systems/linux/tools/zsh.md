# Zsh & Oh-My-Zsh

> 🔗 [Zsh](https://www.zsh.org/)
>
> 🔗 [Oh My Zsh](https://github.com/ohmyzsh/ohmyzsh)

```bash
sudo apt install -y zsh fonts-powerline
sudo chsh -s $(which zsh) $(whoami)

zsh
```

## [OhMyZsh](https://github.com/ohmyzsh/ohmyzsh)

```bash
sh -c "$(wget -O- https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

# Auto-suggestions
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions

# Syntax highlighting
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting

# Powerlevek10k Theme
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/themes/powerlevel10k
```

Open the `.zshrc` file for editing.

```bash
nano ~/.zshrc
```

- Locate the line that sets the Zsh theme and change it to use Powerlevel10k

```bash
ZSH_THEME="powerlevel10k/powerlevel10k"
```

- Add or update the `plugins` line to include additional Zsh plugins

```bash
plugins=(zsh-autosuggestions zsh-syntax-highlighting)
```

- Add the following lines before the `source ...oh-my-zsh.sh` command

```bash
# Fix errors
ZSH_DISABLE_COMPFIX="true"

# Skip all aliases, in lib files and enabled plugins
zstyle ':omz:*' aliases no
```

Save the file, exit and restart `zsh` to apply the changes and configure Powerlevel10k Theme.

```bash
zsh
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

Set Custom **Aliases**.

```bash
nano ~/.oh-my-zsh/custom/aliases.zsh
# Paste your custom aliases
```

- `e.g.` of my custom aliases

```bash
# Alias to update the system
alias updateos='sudo sh -c "sudo apt update && sudo apt -y upgrade && sudo apt -y autoremove"'

# Additional Aliases
alias clipcopy='xclip -selection clipboard'
alias df='df -h'
alias diff='diff --color=auto'
alias dir='dir --color=auto'
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'
alias grep='grep --color=auto'
alias h='history 100'
alias history=omz_history
alias hl='history | less'
alias hs='history | grep'
alias hsi='history | grep -i'
alias ipa='ip -br -c a'
alias l='exa -lah'
alias la='ls -A'
alias ll='l -T'
alias ls='ls -lh --color=auto'
alias mobsf='docker run -it --rm --name mobsf -p 8000:8000 -v ~/docker/mobsf:/home/mobsf/.MobSF opensecurity/mobile-security-framework-mobsf:latest'
alias p3='python3'
alias ports='ss -lpntu'
alias python='python3'
alias sudo='sudo -v; sudo '
alias ugq='ugrep --pretty --hidden -Qria'
alias vdir='vdir --color=auto'
```

---

