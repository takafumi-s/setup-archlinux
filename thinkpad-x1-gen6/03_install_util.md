# Install Util#

## Login Manager

1. install `lightdm`
```
sudo pacman -S lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings
```
1. `Applictions`->`Settings`->`LightDM GTK Greeter settings`から好きなように設定

## Lock laptop on lid closed

1. install
```
sudo pacman -S light-locker
```
1. `Applictions`->`Settings`->`Power Manager`
   - `Laptop Lid`->`When laptop lid is closed`
      - `Switch off display`に変更する

## NTP

1. install
   ```
   sudo pacman -S chrony
   ```
1. set datetime
   ```
   sudo systemctl start chronyd
   ```
