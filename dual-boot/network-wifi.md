# network wifi

  - Begin with a connection by wired LAN.
  
## Setup AUR if not yet.

  - Regist repository
  ```
  #cat <<'EOS' >> /etc/pacman.conf

  [archlinuxfr]
  SigLevel = Never
  Server = http://repo.archlinux.fr/$arch
  EOS
  ```
  
  - Install yaourt
  ```
  # pacman -Syy
  # pacman -S yaourt
  ```

## Install netctl and wifi-menu

  - Notice: that it conflicts with dhcpcd.
  
  - Install
  ```
  # pacman -S dialog
  ```
  - Enable netctl
  ```
  # systemctl enable netctl
  ```
  - Setup WiFi
  ```
  # wifi-menu
  ```
  
  - reboot.
