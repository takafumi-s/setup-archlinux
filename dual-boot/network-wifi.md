# network wifi

  - Begin with a connection by wired LAN.
  
  - https://wiki.archlinuxjp.org/index.php/ワイヤレス設定
  
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

## Setup Network driver

  - broadcom:https://www.archlinux.org/packages/community/x86_64/broadcom-wl-dkms/
  ```
  # yaourt -S broadcom-wl-dkms
  ```

## Install netctl and wifi-menu

  - netctl: https://wiki.archlinuxjp.org/index.php/Netctl
  - dhcpd: https://wiki.archlinuxjp.org/index.php/Dhcpcd
  
  - Install
  ```
  # pacman -S dialog
  # pacman -S netctl
  # pacman -S wifi-menu
  
  # ?Is not "dhcp" itself, something in it necessary? 
  # pacman -S dhcpcd
  ```
  - Enable netctl
  ```
  # pacman -S netctl
  ```
  - Setup WiFi
  ```
  # wifi-menu
  ```
  
  - reboot.
