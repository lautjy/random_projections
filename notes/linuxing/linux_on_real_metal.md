# Linux on Real Metal
*- issues, configs, drivers*

Linux not in VM, but for realsies on Dell Latitude E6430 causes more headaches than just a VirtualBox setup.

## Memories of installing Debian on real metal
1. Create Debian USB stick (KVG)
2. Sometimes you need binary drivers on a USB for say a WiFi
    * find the deb-file that has the binaries, download, open, add to root of a USB
    * file needed was `iwlwifi-6000-4.ucode`
3. So during install I had to switch USB sticks, so that the installer mounted the driver USB correctly. After that installation failed, root-cause being that `/cdrom` was not remounted.
    * You fix this by hitting `ALT+F2` to open console
    * `mount /dev/sdc1 /cdrom`
        - The device for the usb drive may vary. For me it was `/dev/sdc1`.
    * then `ALT+F1` to get back to the install
    * Note: on Debian Stretch just inserted USB to empty slot, and it found the `.ucode` file

I have no idea why the installer isn't smart enough to know to either mount the install media in /cdrom itself of look for it in /target/media/cdrom where it already had it mounted.


## Wifi on Dell Latitude E5430 with Debian Stretch
Find stuff out about the device and drivers

```
lspci -k | less
dmesg | grep fimrware
dmesg | grep iwlwifi
dmesg | grep usbcore
```

Shows our device:

Intel Corporation Centrino Ultimate-N 6300
  * (rev 35)
  * 3x3 AGN
  * iwlwifi

```
# Note: wlan0 is now wlp2s0 due to systemd predictable naming scheme

# Turns out wifi driver "just works".
# Prolly because we provided .ucode -file via USB stick when installing.

# See link status
iw dev wlp2s0 link

# scan networks
iw dev wlp2s0 scan | less
# or
iwlist scan | less


## Create wpa_supplicant.conf that matches you ssid from above list
nano /etc/wpa_supplicant/wpa_supplicant.conf

groupadd wheel
usermod -aG wheel $USERNAME

# Start wifi device and wpa_supplicant
ip link set dev wlp2s0 up
wpa_supplicant -B -i wlp2s0 -c /etc/wpa_supplicant/wpa_supplicant.conf
# that worked. Got "Successfully initialized wpa_supplicant"

```


```
# Is wifi linked?
iw dev wlp2s0 link
# this gives yes: we have SSID, freq, signal strength, etc.
ip link
```

Get that lovely (non-static) IP address

```
dhclient wlp2s0
```

### Starting wifi once it works

```
ip link set dev wlp2s0 up
wpa_supplicant -B -i wlp2s0 -c /etc/wpa_supplicant/wpa_supplicant.conf
dhclient wlp2s0
```




## Synaptics touchpad setup
Configuration snippets can be added to Xorg config.
Can't remember how one is supposed to know to do this, but just create a new
file at `/etc/X11/xorg.conf.d/50-synaptics.conf` and that will be picked up.
Very "convenient".
To get the synaptic touchpad config together, just add this file:

```
# /etc/X11/xorg.conf.d/50-synaptics.conf
Section "InputClass"
    Identifier  "touchpad"
    Driver      "synaptics"
    MatchIsTouchpad "on"
        Option      "LockedDrags"       "on"
        Option      "VertTwoFingerScroll"   "on"
        Option      "HorizTwoFingerScroll"  "on"
        # Circular scrolling is actually useless
        Option      "CircularScrolling" "off"
        # Option      "CircScrollDelta"   "0.15"
        Option      "VertScrollDelta"   "20"
        Option      "HorizScrollDelta"  "20"
        # Stop inertial scrolling
        Option      "CoastingSpeed"     "0"
EndSection
```

That enables vertical scrolling (was off by default), and nice circular scrolling - cool bonus.
Though on the long run the circular scrolling was just an inconvenience.

To try the settings on the fly you can use the `synclient` command: `synclient VertScrollDelta=13`.
