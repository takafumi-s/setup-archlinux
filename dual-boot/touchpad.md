# touchpad

## toggle setting

* Prepare a keyboard shortcut for starting the terminal.

1. Check device name

    ```
    $ libinput list-devices | grep -i touchpad
    ```
    e.g.
    ```
    Device:           DLL0665:01 06CB:76AD Touchpad
    ```

2. Check xinput props

    ```
    $ xinput --watch-props "DLL0665:01 06CB:76AD Touchpad"
    ```
    e.g.
    ```
    Device 'DLL0665:01 06CB:76AD Touchpad':
        Device Enabled (142):   1
        Coordinate Transformation Matrix (144):    1.000000, 0.000000, 0.000000, 0.000000, 1.000000, 0.000000, 0.000000, 0.000000, 1.000000
        libinput Tapping Enabled (277):    1
        libinput Tapping Enabled Default (278):    0
        libinput Tapping Drag Enabled (279):    1
        libinput Tapping Drag Enabled Default (280):    1
        libinput Tapping Drag Lock Enabled (281):    0
        libinput Tapping Drag Lock Enabled Default (282):    0
        libinput Tapping Button Mapping Enabled (283):    1, 0
        libinput Tapping Button Mapping Default (284):    1, 0
        libinput Accel Speed (285):    0.000000
        libinput Accel Speed Default (286):    0.000000
        libinput Natural Scrolling Enabled (287):    1
        libinput Natural Scrolling Enabled Default (288):    0
        libinput Send Events Modes Available (262):    1, 1
        libinput Send Events Mode Enabled (263):    0, 0
        libinput Send Events Mode Enabled Default (264):    0, 0
        libinput Left Handed Enabled (289):    0
        libinput Left Handed Enabled Default (290):    0
        libinput Scroll Methods Available (291):    1, 1, 0
        libinput Scroll Method Enabled (292):    1, 0, 0
        libinput Scroll Method Enabled Default (293):    1, 0, 0
        libinput Click Methods Available (294):    1, 1
        libinput Click Method Enabled (295):    1, 0
        libinput Click Method Enabled Default (296):    1, 0
        libinput Middle Emulation Enabled (297):    0
        libinput Middle Emulation Enabled Default (298):    0
        libinput Disable While Typing Enabled (299):    0
        libinput Disable While Typing Enabled Default (300):    1
        Device Node (265):    "/dev/input/event11"
        Device Product ID (266):    1739, 30381
        libinput Drag Lock Buttons (301):    <no items>
        libinput Horizontal Scroll Enabled (302):    1
    ```
1. Change setting

    - Toggle touchpad device
      ```
      # Disabled
      $ xinput --set-prop "DLL0665:01 06CB:76AD Touchpad" "Device Enabled" 0
      # Enabled
      $ xinput --set-prop "DLL0665:01 06CB:76AD Touchpad" "Device Enabled" 1
      ```
    - Toggle tapping
      ```
      # Disabled
      $ xinput --set-prop "DLL0665:01 06CB:76AD Touchpad" "libinput Tapping Enabled" 0
      # Enabled
      $ xinput --set-prop "DLL0665:01 06CB:76AD Touchpad" "libinput Tapping Enabled" 1
      ```
    - etc...

## Toggle device script

```sh
#!/bin/bash

DEVICE='DLL0665:01 06CB:76AD Touchpad'
PROP='Device Enabled'

enable() {
    xinput --set-prop "$DEVICE" "$PROP" 1
    echo "$DEVICE enabled."
}
disable() {
    xinput --set-prop "$DEVICE" "$PROP" 0
    echo "$DEVICE disabled."
}

case "$1" in
    enable|e|1) enable
        ;;
    disable|d|0) disable
        ;;
    *) echo 'error) set option'
        ;;
esac
```
