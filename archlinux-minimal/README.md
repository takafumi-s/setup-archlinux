# archlinux-minimal

## Install System

* Set jp106 keyboard & font.
```
# loadkeys jp106
# setfont Lat2-Terminus16
```

* Check network
```
ping -c 3 8.8.8.8
```

* Create partition
```
# gdisk /dev/sda
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
Hex code or GUID (L to show codes, Enter = 8300): Enter
Changed type of partition to 'Linux filesystem'

Command (? for help): n
Permission number (2-128, default 2): 3
First sector     : Enter
Last sector      : +1G
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

* format & mount partition.
```
# mkfs.vfat -F32 /dev/sda1
# mkfs.ext4 /dev/sda3
# mount /dev/sda3 /mnt
# mkdir -p /mnt/boot
# mount /dev/sda1 /mnt/boot
```

* create swap
```
# mkswap /dev/sda2
# swapon /dev/sda2
```

* Install system.
```
# vi /etc/pacman.d/mirrorlist  # Move Japanese server to the top
# pacman -Syy
# pacstrap /mnt base base-devel
```

* Create fstab
```
# genfstab -U /mnt >> /mnt/etc/fstab
```

* chroot
```
# arch-chroot /mnt /bin/bash
# vi /etc/locale.gen # Uncomment en_US.UTF-8, ja_JP.UTF-8
# locale-gen
# echo LANG=en_US.UTF-8 > /etc/locale.conf
# export LANG=en_US.UTF-8
```

* Change keymap to jp.
```
# cat <<EOF > /etc/vconsole.conf

KEYMAP=jp106
FONT=lat9w-16
EOF
```

* Set timezone to Tokyo.
```
# ln -s /usr/share/zoneinfo/Asia/Tokyo /etc/localtime
# hwclock --systohc --utc
```

* Enable dhcp
```
# systemctl enable dhcpcd
```

* Set password
```
# passwd
```

## Install BootLoader

* Install GRUB
```
# pacman -S grub dosfstools efibootmgr
# grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=arch_grub --recheck
# grub-mkconfig -o /boot/grub/grub.cfg
# mkdir /boot/EFI/boot
# cp /boot/EFI/arch_grub/grubx64.efi  /boot/EFI/boot/bootx64.efi
```

* reboot
```
# exit
# shutdown -h now
```

* Unmount install disc

* VirtualBox setting  
![VirtualBox-System-Setting](https://github.com/takafumi-s/vagrant-archlinux/blob/master/archlinux-minimal/img/virtualbox-system-efi.png?raw=true "Check VirtualBox -> System -> Enable EFI")

* restart machine

## Setting wheel group

* wheel group setting
```
# visudo
## Uncomment to allow members of group wheel to execute any command
%wheel ALL=(ALL) ALL   # <- Uncomment
```

## Vagrant setting

### Create vagrant user

* Create vagrant user.
```
# useradd -m -G wheel -s /bin/bash vagrant
# passwd vagrant
```

* Setting sudo
```
# echo -n 'vagrant ALL=(ALL) NOPASSWD: ALL' > /etc/sudoers.d/vagrant
```

###  Install ssh

* install
```
# pacman -S openssh
# systemctl enable sshd
```

* ssh authorized_keys
```
# mkdir ~/.ssh 
# curl -L https://raw.github.com/mitchellh/vagrant/master/keys/vagrant.pub -o ~/.ssh/authorized_keys
# chmod 0700 ~/.ssh
# chmod 0600 ~/.ssh/authorized_keys
# cp -a ~/.ssh /home/vagrant/
# chown -R vagrant:vagrant /home/vagrant/.ssh
```

* login vagrant user

### Install Guestutils

* As necessary, install other kernel.

    * Install kernel
        * lts
        ```
        $ # sudo pacman -S linux-lts linux-lts-headers
        ```
        * zen
        ```
        $ sudo pacman -S linux-zen linux-zen-headers
        ```
    * GRUB setting.
    ```
    $ sudo grub-mkconfig -o /boot/grub/grub.cfg
    $ sudo reboot
    ```

* Install guestutils
```
$ sudo pacman -S virtualbox-guest-utils
$ # Select 1 for lts,zen kernel
$ # Select 2 for linux kernel
```

* Enable vboxservice
```
$ sudo systemctl enable vboxservice
$ sudo reboot
```

## Vagrant operation

* Add vagrant.box
```
vagrant package --base archlinux --output archlinux-minimal.box
vagrant box add --name archlinux-minimal archlinux-minimal.box
```

* Create Vagrantfile
```
vagrant init archlinux-minimal
```
