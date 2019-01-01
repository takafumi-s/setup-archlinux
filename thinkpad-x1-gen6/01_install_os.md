# OS Install


## Dual Boot準備

### Windowsのバックアップ

1. ファクトリリカバリを作っておく

### パーティションを作成

1. 空き領域のあるパーティションを縮小して、未設定領域を作る
1. `/`, `home`, `boot`用のパーティションを作成
1. フォーマットはlinux側で実施するの

### インストールメディアを作成

1. https://wiki.archlinuxjp.org/index.php/USB_インストールメディア##Rufus_.E3.82.92.E4.BD.BF.E3.81.86 を参考に
   1. Rufusをダウンロード
   1. https://www.archlinux.org/download/ からarchisoをダウンロード
   1. Rufusを使ってブートUSBを作成する

### デフォルトのブートローダー(UEFI)の設定を変更

1. 再起動
1. ロゴ表示前からF12を連打
1. UEFI設定画面に
1. secure bootをdisableに変更する

### USBメディアからarchiso起動

1. 有線LANはつながるはず
1. F12でboot menuを表示
1. インストールUSBから起動する

### Check network

1. Check
   ```sh
   ping -c 3 1.1.1.1
   ```

## Prepare Partition

### Create Partition

1.
   ```sh
   gdisk /dev/nvme0n1

   GPT fdisk\ (gdisk) version 1.0.1

   Partition table scan:
     MBR: not present
     BSD: not present
     APM: not present
     GPT: not present

   Creating new GPT entries.

   Command (? for help): o
   This option deletes all partitions and creates a new protective MBR.
   Proceed? (Y/N): Y

   Command (? for help): n
   Permission number (1-128, default 1): 1
   First sector     : Enter
   Last sector      : +512M
   Current type is 'Linux filesystem'
   Hex code or GUID (L to show codes, Enter = 8300): EF00
   Changed type of partition to 'Linux filesystem'

   Command (? for help): n
   Permission number (2-128, default 2): 2
   First sector     : Enter
   Last sector      : +1G
   Current type is 'Linux filesystem'
   Hex code or GUID (L to show codes, Enter = 8200): Enter
   Changed type of partition to 'Linux filesystem'

   Command (? for help): n
   Permission number (2-128, default 3): 3
   First sector     : Enter
   Last sector      : + 50G
   Current type is 'Linux filesystem'
   Hex code or GUID (L to show codes, Enter = 8300): Enter
   Changed type of partition to 'Linux filesystem'

   Command (? for help): n
   Permission number (2-128, default 4): 4
   First sector     : Enter
   Last sector      : + 127G
   Current type is 'Linux filesystem'
   Hex code or GUID (L to show codes, Enter = 8300): Enter
   Changed type of partition to 'Linux filesystem'

   Command (? for help): w

   Final checks complete. About to write GPT data. THIS WILL OVERRITE EXISTING
   PARTITIONS!!

   Do you want to proceed? (Y/N): Y
   OK: writing new GUID partition table (GPT) to /dev/sda.
   The operation has completed successfully
   ```

### Format Partition

1.
   ```sh
   # BOOT FAT32で作成
   mkfs.vfat -F32 /dev/nvme0n1p5

   # SWAP 作成
   mkswap /dev/nvme0n1p6

   # ROOT 作成
   mkfs.ext4 /dev/nvme0n1p7

   # HOME 作成
   mkfs.ext4 /dev/nvme0n1p8
   ```

### Mount Partition

1.
   ```sh
   # ROOT
   mount /dev/nvme0n1p7 /mnt/

   # mount point 作成
   mkdir /mnt/boot /mnt/home

   # BOOT
   mount /dev/nvme0n1p5 /mnt/boot

   # HOME
   mount /dev/nvme0n1p8 /mnt/home
   ```

### Swap On

1. 
   ```sh
   swapon /dev/nvme0n1p6 
   ```


### Install Kernel

- 
   ```sh
   vi /etc/pacman.d/mirrorlist  # Move Japanese server to the top
   pacman -Syy
   pacstrap /mnt base base-devel
   ```

### Create fstab

1. 
   ```sh
   genfstab -U /mnt >> /mnt/etc/fstab
   ```

## arch-chroot ~ boot loader

### arch-chroot

1. 
   ```sh
   arch-chroot /mnt /bin/bash
   ```

### Locale setting

1. 
   ```sh
   vi /etc/locale.gen # Uncomment en_US.UTF-8, ja_JP.UTF-8
   locale-gen
   echo LANG=en_US.UTF-8 > /etc/locale.conf
   export LANG=en_US.UTF-8
   ```

### Set timezone to Tokyo

1. 
   ```
   ln -s /usr/share/zoneinfo/Asia/Tokyo /etc/localtime
   hwclock --systohc --utc
   ```

### Set root password

1. 
   ```
   passwd
   ```

### Boot Loader

- systemd-bootを使う
   - https://wiki.archlinuxjp.org/index.php/Systemd-boot

1. Setting Boot Loader - systemd-boot 
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
 
## zen kernel

1. 他のコンパイル済みカーネルをインストール（しない場合はデフォルトのlinux kernelになる）
   - https://wiki.archlinuxjp.org/index.php/カーネル

### Install kernel-zen

1.
   ```
   pacman -S linux-zen linux-zen-headers
   ```

### Add Boot Loader

1.
   ```
   vi /boot/loader/entries/arch-zen.conf
   # title   Arch Linux Zen
   # linux   /vmlinuz-linux-zen
   # initrd  /intel-ucode.img
   # initrd  /initramfs-linux-zen.img
   # options root=PARTUUID=PARTUUID rw noefi
   ```

### Setting default

1.
  ```
  vi /boot/loader/loader.conf
  # default  arch-zen
  # timeout  3
  # editor  0
  ```

---

rebootすると、インストールしたarchが起動する
