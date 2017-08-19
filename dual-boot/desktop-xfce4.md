# desktop xfce4

## Install xfce4

* graphic card driver
```
# pacman -S xf86-video-intel
```

* xorg
```
# pacman -S xorg-server xorg-apps xorg-xinit xorg-twm xorg-xclock xterm mesa
```

* xfce4
```
pacman -S xfce4 xfce4-goodies
```

## Install util

* display manager
```
# pacman -S lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings 
```

* manage user dir
```
# pacman -S xdg-user-dirs-gtk
```

* dir util
```
# pacman -S gamin gvfs
```

* archiver
```
# pacman -S xarchiver zip unzip
```

* sound
```
# pacman -S pulseaudio pulseaudio-alsa xfce4-pulseaudio-plugin pavucontrol
```

* font
```
# pacman -S noto-fonts-cjk
```

* keyboard
```
# pacman -S fcitx-im fcitx-configtool fcitx-mozc
```

* browser
```
 # pacman -S chromium firefox firefox-i18n-ja
 ```


## Test

  - xorg
  ```
  startx
  ```
  - xfce4
  ```
  startxfce4
  ```
  - lightdmはコマンドだけでは、エラーになる模様

## Setting

  - lightdm
  ```
  systemctl enable lightdm.service
  ```
