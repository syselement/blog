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
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```

```bash
nano ~/.zshrc

# Change ZSH_THEME
ZSH_THEME="powerlevel10k/powerlevel10k"
	
# add custom plugins in the plugins
plugins=(git zsh-autosuggestions zsh-syntax-highlighting)

# Fix errors (before source zsh)
ZSH_DISABLE_COMPFIX="true"


### At the end of the file add following lines ###

# Skip all aliases, in lib files and enabled plugins
zstyle ':omz:*' aliases no

# Save the file, exit and re-run zsh to configure Powerlevel10k Theme
zsh
```

- Set the same `ohmyzsh` config for the `root` user

```bash
USER=root
USER_HOME_PATH=~$USER
sudo ln -s $HOME/.zshrc ${USER_HOME_PATH}/.zshrc
sudo ln -s $HOME/.oh-my-zsh ${USER_HOME_PATH}/.oh-my-zsh
sudo ln -s $HOME/.p10k.zsh	${USER_HOME_PATH}/.p10k.zsh
sudo chsh -s $(which zsh) $USER
```

- Set Custom **Aliases**

```bash
nano /home/user/.oh-my-zsh/custom/aliases.zsh
# Paste your custom aliases
```

```bash
# Alias to update the system
alias updateos='sudo sh -c "sudo apt update && sudo apt -y upgrade && sudo apt -y autoremove"'

# Additional Aliases
alias clipcopy="xclip -selection clipboard"
alias df="df -h"
alias diff='diff --color=auto'
alias dir='dir --color=auto'
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'
alias grep='grep --color=auto'
alias ipa='ip -br -c a'
alias l='exa -lah'
alias la='ls -A'
alias ll='l -T'
alias ls='ls -lh --color=auto'
alias mobsf='docker run -it --rm --name mobsf -p 8000:8000 -v ~/docker/mobsf:/home/mobsf/.MobSF opensecurity/mobile-security-framework-mobsf:latest'
alias p3="python3"
alias ports="ss -lpntu"
alias python="python3"
alias sudo='sudo -v; sudo '
alias ugq="ugrep --pretty --hidden -Qria"
alias vdir='vdir --color=auto'
```

