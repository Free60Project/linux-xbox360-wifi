# Xbox 360 S Wi-Fi: Marvell 88W8786U (USB 045e:0765) on Linux

## Caveats

- Power save is rejected by firmware

## Build
```
cd linux-6.18.11
git apply <libxenon>/linux-kernel-xbox360/patch-6.18-xenon0.30.diff
git apply patches/0001-*.patch
export ARCH=powerpc CROSS_COMPILE=xenon-
make olddefconfig && make -j4 zImage modules
```
Wi-Fi modules only: `make -j4 M=drivers/net/wireless/marvell/mwifiex modules`

Needs `CONFIG_CFG80211=m CONFIG_MWIFIEX=m CONFIG_MWIFIEX_USB=m`.

## Run
```
# Load kernel modules
insmod cfg80211.ko; insmod mwifiex.ko; insmod mwifiex_usb.ko
# Or for debugging
insmod mwifiex.ko debug_mask=0x100000ff

# Setup interface
ip link set mlan0 up; iw dev mlan0 scan | grep SSID

# Connect to network
wpa_passphrase 'SSID' 'PASS' > wpa.conf     # add: ctrl_interface=/run/wpa_supplicant
wpa_supplicant -B -i mlan0 -D nl80211 -c wpa.conf
```

dmesg success:
```
usb 1-4: info: Xbox adapter is in boot ROM mode      (or: already running)
usb 1-4: info: Xbox adapter booted its flash firmware
usb 1-4: WLAN FW is active
```
