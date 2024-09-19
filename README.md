SailfishOS HADK Scratchpad for lenovo_karatep
- based on lineage/hybris-18.1

Status

| **Feature**           | **Status**             |
|-----------------------|------------------------|
| **Linux Kernel**      | 3.18                   |
| **Display**           | Y                      |
| **Touch**             | Y                      |
| **Notification LED**  | Unknown (lights up)    |
| **Audio**             |                        |
| - Loudspeaker         | Y                      |
| - 3.5mm               | N (detected)           |
| - Earpiece            | Unknown                |
| **NFC**               | NA                     |
| **Bluetooth**         | N                      |
| **GSM**               |                        |
| - Slot 1              |                        |
|   - Signal            | Y                      |
|   - Call              | Unknown                |
|   - Data              | N                      |
|   - SMS               | Unknown                |
|   - VoLTE             | jolla proprietary      |
| - Slot 2              | Network: Denied        |
| **WLAN**              |                        |
| - Connect             | N                      |
| - Hotspot             | N                      |
| **GPS**               | Unknown                |
| **Camera**            |                        |
| - Front               | Y (worked once)        |
| - Rear                | Y (worked once)        |
| - Flash               | Unknown                |
| **Fingerprint**       | N                      |
| **Sensors**           | Unknown (rotation works)|
| - ALS                 |                        |
| - PS                  |                        |
| - Accel.              |                        |
| - Gyro.               |                        |
| - Magne.              |                        |
| **Keys**              |                        |
| - Power               | Y                      |
| - Vol+                | Y                      |
| - Vol-                | Y                      |
| - Soft keys           |                        |
|   - Back              | Y                      |
|   - Home              | N                      |
|   - Nav               | N                      |
| **Vibra**             | Y                      |
| **Haptics**           | NA                     |
| **Power Mgmt.**       | Unknown                |
| **RTC alarms**        | Unknown                |
| **USB**               |                        |
| - Net                 | Y                      |
| - Data                | Y                      |
| - Charge              | Y                      |
| **FM Radio**          | Unknown                |

To fix:
- killall vndservicemanager on boot
- systemctl restart ofono on boot
- 3.5mm audio routing
- RIL flaky
- Cameras flaky
- Rahul, [9/17/24 2:20 AM]
Okay, so I tried to restart the device. This time bluebinder is unmasked. wlan0 doesn't show up. modprobe wlan says no device named wlan. BT MAC is okay, however bluebinder doesn't start, stays at "activating". Masked bluebinder and restarted, wlan works fine. Also, BT works when I start bluebinder service after boot

Rahul, [9/17/24 2:21 AM]
Could this be related to wlan mac being incorrect?

Pixel ratio is 1.6

Notes:
- Updated/recent hybris patches are at hadk-hot. Refer them along with/instead of hadk-faq. https://sailfishos.wiki/books/hadk/page/hadk-hot#bkmrk-common
- <mal> only relevant part from 16 in 18.1 base is cloning libhybris
- Add LOS devicesettings: https://github.com/tanvirr007/CustomROM_build_guide_aosp
- Kernel bootflags to be set in BoardConfig.mk
- Make selinux permissive by setting selinux=1 in kernel bootflags
- Copy selinux files from /vendor on device while applying hybris-17.1 patched mentioned in hadk-faq. Do not symlink, replace with actual files (Thanks @mal)
- /system -> / system partition is actually android root partition and the actual system is in /system/system so replacing /system with / in fstab. fstab is at: device/lenovo/karate-common/rootdir/etc/fstab.qcom (Thanks @mal)
- Disable audit logs by setting audit=0 in kernel bootflags (Thanks @elros34)
- Stop system init to prevent bootloop: Create file init_enter_debug2 to sailfish os root i.e. in /data/.stowaways/sailfishos/init_enter_debug2 (Thanks @mal)
- Files on dcd/sparse/ gets copied onto the device.
- Symlink /apex .so libraries into /odm on device or /sparse/odm on dev machine.
- Spam "Expecting header 0x53595354 but found 0x564e4452. Mixing copies of libbinder?". Kill that process to restart vndservicemanager. Need to find a permanent fix. Dirty fix: "killall vndservicemanager" (at boot)
- Missing battery info? Add hw-settings.ini to specify sensor information. Refer hadk-faq
- Sailjail fixes: Enable CONFIG_UTS_NS, CONFIG_IPC_NS, CONFIG_USER_NS, CONFIG_PID_NS, CONFIG_NET_NS, CONFIG_NF_CONNTRACK_NETBIOS_NS in karatep_defconfig

Resources:
- (ofono conflict) https://sailfishos.wiki/books/hadk/page/hadk-hot https://irclogs.sailfishos.org/logs/%23sailfishos-porters/%23sailfishos-porters.2023-02-03.log.html
- (setup selinux permissive) https://piggz.co.uk/sailfishos-porters-archive/index.php?log=2024-08-20.txt#line64
- (patch apex) If apexd-bootstrap fails because of not updatable/flattened apexes here is experimental fix: https://paste.debian.net/hidden/a2302262/ (Thanks @elros34)

Commands:
- (Update Platform SDK, Tooling, and Target): "sdk-foreach-su -ly ssu re some_version" then "sdk-foreach-su -ly zypper ref" and finally "sdk-foreach-su -ly zypper dup"

Reverse internet tethering:
- Follow HADK
- https://forum.sailfishos.org/t/using-internet-over-rndis-usb-computer-reverse-tethering/10104/6
