# Mac

## Environment

- package manager: homebrew
    - `/opt/homebrew/bin`
- git: `brew install git`
    - config user.name and user.email
    - generate ssh key and connect to GitHub
    - when first push, answer `yes` to update the list of known hosts
- python: `brew install python`
    - `python3 --version`
    - `pip3 --version`
- mkdocs, material and my extensions: `pip3 install -r library.txt`
    - packages installed with pip is not in PATH (not included in bin of brew):
        - write `export PATH=\$PATH:/Users/minjoker/Library/Python/3.9/bin` into `.zshrc`

## Terminal

- iTerm2: `brew install iTerm2`
    - color schemes: gruvbox-dark
    - font: MesloLGS NF (recommended by powerlevel10k)
- shell: zsh (come with mac) and oh-my-zsh (framework for managing zsh configuration)
    - install: you may need wget or curl before installing oh-my-zsh
    - theme: powerlevel10k
    - plugin: git clone to `~/.oh-my-zsh/custom/plugins`
        - zsh-autosuggestions
        - zsh-history-substring-search
        - zsh-syntax-highlighting
    - bindkey: `~/.oh-my-zsh/lib/key-binding.zsh`

## Others

- How to bind command+left to begin-of-line in terminal?
    1. iTerm2->profiles->keys->key mapping, add a new mapping of command+left, with send escape sequence like `begin`
    2. `bindkey '^[begin' beginning-of-line`

## Reference

- [https://zhuanlan.zhihu.com/p/550022490](https://zhuanlan.zhihu.com/p/550022490)