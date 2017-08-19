# dual-boot - dell xps13 (9343)

  - 基本はここを参照 https://wiki.archlinuxjp.org/index.php/Dell_XPS_13_(9343)
  - xps13の他世代のwikiの情報も参考に
  
  - xps13のWifi moduleがarchisoに入っていないため、有線LANで接続する
    - インストールメディアにmoduleソースを入れてbuildすればいいのかもしれないが、途中で挫折した
  

## Windowsのバックアップを取る

  - ファクトリリカバリを作っておく。作り方はdellのマニュアルを参照。

## パーティションを作成

  - 空き領域のあるパーティションを縮小して、未設定領域を作る
  - `/`, `home`用のパーティションを作成（`/`だけでも可）
  - フォーマットはlinux側で実施するの

## インストールメディアを作成

### Windows

  - https://wiki.archlinuxjp.org/index.php/USB_インストールメディア##Rufus_.E3.82.92.E4.BD.BF.E3.81.86 を参考に
    1. Rufusをダウンロード
    1. https://www.archlinux.org/download/ からarchisoをダウンロード
    1. Rufusを使ってブートUSBを作成する

## デフォルトのブートローダー(UEFI)の設定を変更

  1. 再起動
  1. ロゴ表示前からF2を連打
  1. UEFI設定画面に
  1. secure bootをdisableに変更する

## USBメディアからarchiso起動

  1. インストールメディアをPCに挿して、起動
  1. F12でboot menuを表示
  1. インストールメディアを起動する

## Install System by archiso

  - Set jp106 keyboard & font.  
  ```
  loadkeys jp106
  setfont Lat2-Terminus16
  ```
  
  - Check network
  ```
  ping -c 3 8.8.8.8
  ```
  
  - Format Drive
  ```
  # show drive
  lsblk
  
  # format
  mkfs.ext4 /dev/sdaX
  mkfs.ext4 /dev/sdaY
  ```
  
  - Mount drive
  ```
  mount /dev/sdaX /mnt
  
  mkdir /mnt/boot /mnt/home
  mount /dev/sdaZ /mnt/boot
  mount /dev/sdaY /mnt/home
  ```
  
  - Install system.
  ```
  vi /etc/pacman.d/mirrorlist  # Move Japanese server to the top
  pacman -Syy
  pacstrap /mnt base base-devel
  ```
  
  - Create fstab
  ```
  genfstab -U /mnt >> /mnt/etc/fstab
  ```

## Install System by arch-chroot

  - arch-chroot
  ```
  arch-chroot /mnt /bin/bash
  ```
  
  - Locale setting
  ```
  vi /etc/locale.gen # Uncomment en_US.UTF-8, ja_JP.UTF-8
  locale-gen
  echo LANG=en_US.UTF-8 > /etc/locale.conf
  export LANG=en_US.UTF-8
  ```
  
  - Change keymap to jp.
  ```
   cat <<EOF > /etc/vconsole.conf

  KEYMAP=jp106
  FONT=lat9w-16
  EOF
  ```

  - Set timezone to Tokyo.
  ```
  ln -s /usr/share/zoneinfo/Asia/Tokyo /etc/localtime
  hwclock --systohc --utc
  ```
  
  - Enable dhcp
  ```
  systemctl enable dhcpcd
  ```
  
  - Set root password
  ```
  passwd
  ```

## Install Boot Loader

  - GRUBではなく、systemd-bootを使う
    - https://wiki.archlinuxjp.org/index.php/Systemd-boot
  
  - Setting Boot Loader - systemd-boot 
  ```
  bootctl --path=/boot install

  vi /boot/loader/loader.conf
  # default  arch
  # timeout  3
  # editor  0

  cp /usr/share/systemd/bootctl/arch.conf /boot/loader/entries/

  pacman -S intel-ucode
  # Intelのマイクロコード修正もここで入れておく

  blkid -s PARTUUID -o value /dev/nvme0n1p4 >> /boot/loader/entries/arch.conf
  # optionsに設定するrootパーティションのPARTUUIDを追記しておく。手で打ってもOK

  vi /boot/loader/entries/arch.conf
  # title   Arch Linux
  # linux   /vmlinuz-linux
  # initrd  /intel-ucode.img
  # initrd  /initramfs-linux.img
  # options root=PARTUUID=PARTUUID rw noefi
  ```
  
  - 再起動すると、インストールしたarchが起動する
