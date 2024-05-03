# Mac

## Environment

- package manager: homebrew
    - disable auto- upgrade:
        1. `echo 'export HOMEBREW_NO_AUTO_UPDATE=1' >> ~/.zshrc`
        2. `source ~/.zshrc`
- git: `brew install git`
    - config user.name and user.email
    - generate ssh key and connect to GitHub
- python: `brew install python`
    - `python3 --version`
    - `pip3 --version`
- mkdocs, material and my extensions: `pip3 install -r library.txt`
    - packages installed with pip is not in PATH (not included in bin of brew):
        1. `echo "export PATH=\$PATH:/Users/minjoker/Library/Python/3.9/bin" >> ~/.zshrc`
        2. `source ~/.zshrc`

## Terminal
