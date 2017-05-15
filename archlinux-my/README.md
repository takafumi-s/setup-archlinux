# archlinux-desktop-xfce4

## Preparation

* use archlinux-desktop-xfce4.box

## AUR repo

```
# cat <<'EOS' >> /etc/pacman.conf

[archlinuxfr]
SigLevel = Never
Server = http://repo.archlinux.fr/$arch
EOS
```

## Util

```
# pacman -S wget
# pacman -S the_silver_searcher # ag command
# pacman -S ctags

## zsh

```
# pacman -S zsh
```
```
# chsh -s /bin/zsh
# chsh -s /bin/zsh vagrant
```

## git

```
# pacman -S git
```

## vim

```
# pacman -S vim
```

## tmux

```
# pacman -S tmux
```

## Program Language

### java

```
# pacman -S jre8-openjdk-headless
```

### scala

```
# pacman -S sbt
```

### node.js

```
# pacman -S nodejs npm
```

### haskell

* https://docs.haskellstack.org/en/stable/install_and_upgrade/#arch-linux
```
# packman -S stack
```
```
$ stack setup
```
```
$ stack install ghc-mod
$ stack install hlint
$ stack install stylish-haskell

### lua

* required vim autocomplete.

```
# pacman -S lua
```
