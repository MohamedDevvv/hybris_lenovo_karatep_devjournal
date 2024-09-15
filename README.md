SailfishOS HADK Scratchpad for lenovo_karatep
- based on lineage/hybris-18.1

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
- Spam "Expecting header 0x53595354 but found 0x564e4452. Mixing copies of libbinder?". Kill that process to restart vndservicemanager. Need to find a permanent fix.

Resources:
- (ofono conflict) https://sailfishos.wiki/books/hadk/page/hadk-hot https://irclogs.sailfishos.org/logs/%23sailfishos-porters/%23sailfishos-porters.2023-02-03.log.html
- (setup selinux permissive) https://piggz.co.uk/sailfishos-porters-archive/index.php?log=2024-08-20.txt#line64
- (patch apex) If apexd-bootstrap fails because of not updatable/flattened apexes here is experimental fix: https://paste.debian.net/hidden/a2302262/ (Thanks @elros34)

Commands:
- (Update Platform SDK, Tooling, and Target): "sdk-foreach-su -ly ssu re some_version" then "sdk-foreach-su -ly zypper ref" and finally "sdk-foreach-su -ly zypper dup"


