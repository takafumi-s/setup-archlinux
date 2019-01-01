# Install Device

## Touchpad

1. デバイスを見つける
   ```
   sudo libinput list-devices | grep -i touchpad -B1 -A18                                                                                                                                       ~/Git/GitHub/tkfmst/setup-archlinux[master]
   ```
   ```
   Device:           SynPS/2 Synaptics TouchPad
   Kernel:           /dev/input/event7
   Group:            8
   Seat:             seat0, default
   Size:             98x53mm
   Capabilities:     pointer gesture
   Tap-to-click:     disabled
   Tap-and-drag:     enabled
   Tap drag lock:    disabled
   Left-handed:      disabled
   Nat.scrolling:    disabled
   Middle emulation: disabled
   Calibration:      n/a
   Scroll methods:   *two-finger edge
   Click methods:    *button-areas clickfinger
   Disable-w-typing: enabled
   Accel profiles:   none
   Rotation:         n/a
   ```
1. 設定値を確認
   ```
   takafumi@archlinux $ sudo xinput --watch-props "SynPS/2 Synaptics TouchPad"
   Device 'SynPS/2 Synaptics TouchPad':
           Device Enabled (151):   1
           Coordinate Transformation Matrix (153): 1.000000, 0.000000, 0.000000, 0.000000, 1.000000, 0.000000, 0.000000, 0.000000, 1.000000
           libinput Tapping Enabled (286): 0
           libinput Tapping Enabled Default (287): 0
           libinput Tapping Drag Enabled (288):    1
           libinput Tapping Drag Enabled Default (289):    1
           libinput Tapping Drag Lock Enabled (290):       0
           libinput Tapping Drag Lock Enabled Default (291):       0
           libinput Tapping Button Mapping Enabled (292):  1, 0
           libinput Tapping Button Mapping Default (293):  1, 0
           libinput Natural Scrolling Enabled (294):       0
           libinput Natural Scrolling Enabled Default (295):       0
           libinput Disable While Typing Enabled (296):    1
           libinput Disable While Typing Enabled Default (297):    1
           libinput Scroll Methods Available (298):        1, 1, 0
           libinput Scroll Method Enabled (299):   1, 0, 0
           libinput Scroll Method Enabled Default (300):   1, 0, 0
           libinput Click Methods Available (301): 1, 1
           libinput Click Method Enabled (302):    1, 0
           libinput Click Method Enabled Default (303):    1, 0
           libinput Middle Emulation Enabled (304):        0
           libinput Middle Emulation Enabled Default (305):        0
           libinput Accel Speed (306):     0.000000
           libinput Accel Speed Default (307):     0.000000
           libinput Left Handed Enabled (308):     0
           libinput Left Handed Enabled Default (309):     0
           libinput Send Events Modes Available (271):     1, 1
           libinput Send Events Mode Enabled (272):        0, 0
           libinput Send Events Mode Enabled Default (273):        0, 0
           Device Node (274):      "/dev/input/event7"
           Device Product ID (275):        2, 7
           libinput Drag Lock Buttons (310):       <no items>
           libinput Horizontal Scroll Enabled (311):       1
   ```
1.  `/etc/X11/xorg.cond.d`に設定ファイルを作成
   `/usr/share/X11/xorg.conf.d/`からコピーして必要箇所を修正する
   `8-`は`SynPS/2 Synaptics TouchPad`のグループが8なので 
   ```
   $ cat /etc/X11/xorg.conf.d/40-touchpad.conf
   Section "InputClass"
           Identifier "Touchpad Setting"
           MatchIsTouchpad "on"
           Driver "libinput"
           Option "Tapping Enabled" "on"
           Option "Natural Scrolling Enabled" "on"
   EndSection
   ```

## Wifi

1. Wifiのinterfaceが立ち上がっている事を確認  
   1. `<>`にUPがあればOK。`state`は実際にWifiに繋がったらUPになる
      ```
      takafumi@archlinux $ ip l | grep wlp2s0
      2: wlp2s0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc mq state DOWN mode DEFAULT group default qlen 1000
      ```
   2. 立ち上がっていなかったら
      ```
      sudo ip l set wlp2s0 up
      ```
1. 接続の管理はNetworkManagerを使う
   ```
   sudo pacman -S network-manager network-manager-applet xfce4-notifyd
   ```
   - dhcpcdとは共存できないそうなので、`systemctl status dhcpcd`でserviceが起動していない事を確認しておく

## Sound

1. install
   ```
   sudo pacman alsa-utils
   ```
1. 以下でauto-muteをdisableにしておく
   ```
   alsamixer
   ```

### pulseaudio

1. 
   ```
   sudo pacman -S pulseaudio pavucontrol
   yay -S pulseaudio-ctl pasystray-git
   ```
