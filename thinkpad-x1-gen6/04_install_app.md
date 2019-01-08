# Install App

## Tool

- 
   ```
   sudo pacman -S rsync
   sudo pacman -S tmux
   ```

## Web Blowser

- 
   ```
   yay -S google-chrome
   sudo pacman -S firefox
   ```

## Archiver

- 
   ```
   sudo pacman -S zip unzip
   ```

## Editor

1. install
   ```
   sudo pacman -S neovim
   pip install pynvim
   ```
1. check
   1. `:checkhealth`を実行
   1. `OK: Latest python3-neovim is installed: x.y.z`となっていればOK

## Image Viewer

- 
   ```
   # Eye Of GNOME
   sudo pacman -S eog
   ```

## Chat

- 
   ```
   yay -S slack-desktop
   ```

## Docker

1. install
   ```
   sudo pacman -S docker
   sudo mkdir /etc/systemd/system/docker.service.d/
   sudo nvim /etc/systemd/system/docker.service.d/docker.conf
   ```
   ```
   [Service]
   ExecStart=
   ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:4243 -H unix:///var/run/docker.sock --default-ulimit nofile=20000:20000
   ```
1. sudoしなくてもdockerが使えるように現在のユーザーをgroupに追加
   ```
   sudo gpasswd -a $USER docker
   ```
1. DOCKER_HOSTを設定
   ```
   export DOCKER_HOST=127.0.0.1:4243
   ```
1. launch
   ```
   sudo systemctl start docker
   ```
   or
   ```
   sudo systemctl enable docker
   ```
1. install docker-compose
   ```
   sudo systemctl -S docker-compose
   ```
