# Intro into chezmoi

## Getting started

We start with a plain debian including curl, git, vim and the zsh.

````dockerfile
FROM debian:11
RUN apt update
RUN apt install curl git vim zsh zplug -y

RUN adduser matthias
USER matthias
WORKDIR /home/matthias
````

Build the docker file and run it. I use ``--rm`` to automatically remove the container after exit.
````shell
docker build . --tag mb.chezmoi.debian:latest
docker run -it --rm mb.chezmoi.debian /bin/zsh
````

When starting the zsh it asks for the Z Shell configuration. Choose option (2) for a initial configuration.

Now we change the theme of the zsh for better readability and to have a obvious change in a dotfile.

````shell
vi .zshrc
````

Change the line ``prompt adam1`` to ``prompt redhat```. Save the file and quit vi.

Reload the configuration and see the theme of the prompt has changed.
````shell
source .zshrc
````

## Configure git

````shell
git config --global --add init.defaultbranch main
git config --global --add user.name "Matthias Baumgart"
git config --global --add user.email "matthias.baumgart@zeiss.com"
````

## Install chezmoi and initialize

````shell
sh -c "$(curl -fsLS get.chezmoi.io)" -- init
source .profile
chezmoi --version
chezmoi git status
ls -la ~/.local/share/chezmoi
````

## Add .zshrc

```shell
chezmoi add .zshrc
cd ~/.local/share/chezmoi
git add dot_zshrc
git commit -m "Add .zshrc"

```

## Create a github repository and push it

- Chezmoi supports private and public repositories
- Do not publish secrets!

````shell
git remote add origin https://github.com/mab/intro-chezmoi.git
git push --set-upstream origin main
````

## Install chezmoi with my values

````shell
sh -c "$(curl -fsLS get.chezmoi.io)" -- init --apply https://github.com/mab/intro-chezmoi.git
````

## Other way to add files to chezmoi

Goal is to add the .gitconfig on the first machine also to chezmoi, push it to the github repository and apply the change to the second machine.

On the first machine:
````shell
mkdir -p ~/.local/share/chezmoi/private_dot_config/private_git
cp ~/.gitconfig ~/.local/share/chezmoi/private_dot_config/private_git/config
git config --list
chezmoi status
chezmoi diff
chezmoi apply
````

On the second machine:
````shell
chezmoi update
git config --list
````


## Optional

### Homebrew

```shell
test -d ~/.linuxbrew && eval "$(~/.linuxbrew/bin/brew shellenv)"
test -d /home/linuxbrew/.linuxbrew && eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"
test -r ~/.bash_profile && echo "eval \"\$($(brew --prefix)/bin/brew shellenv)\"" >> ~/.bash_profile
echo "eval \"\$($(brew --prefix)/bin/brew shellenv)\"" >> ~/.profile
```
