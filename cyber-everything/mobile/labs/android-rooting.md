# Android Rooting Guide

![](.gitbook/assets/android-logo.png)

---

## 🌐 Resources 🔗

> `e.g.` with a Google Pixel phone
>
> 🔗 [Pixel 6a](https://forum.xda-developers.com/f/google-pixel-6a.12605/)
>
> 🔗 [A13 detailed guide to Root & OTA's & pass SafetyNet / Play Integrity](https://forum.xda-developers.com/t/a13-detailed-guide-to-root-otas-pass-safetynet-play-integrity.4530221/)

---

## Introduction

This guide will walk you through the process of rooting your Google Pixel device using Magisk

---

## Prerequisites

Before we begin, make sure you have the following:

- A Google Pixel device (e.g., Pixel 6a).

### Android SDK Platform Tools

- Android SDK Platform Tools installed on your PC. You can install it using the following command:

```bash
sudo apt install -y android-sdk-platform-tools
```

### Developer Options / USB Debugging

- [Developer Options](https://developer.android.com/studio/debug/dev-options) enabled on your device. To do this:
  - Go to **Settings** > **About phone**.
  - Tap on the **Build number** several times to enable `Developer Options`.
  - Enter **System** > **Developer options**.
  - Set the following options:
    - Enable `OEM Unlocking`
    - Disable `Automatic system updates`
    - Enable `USB debugging`
    - Enable `Wireless debugging`
    - Enable `Disable adb authorization timeout`

---

## Unlock Bootloader (Google Devices)

1. Connect your device to your PC and authorize it. Reboot the device into bootloader mode using the following commands:

```bash
# Check for the connected device
adb devices

# Reboot to bootloader 
adb reboot bootloader
```

2. Wait for the Fastboot Mode screen to appear on your device and run the following command in the terminal:

```bash
fastboot flashing unlock
```

3. Use volume keys to select `Unlock the bootloader` option and confirm with the power button.
4. The `Device state` will change to `unlocked`.
5. Start the device and complete the initial setup.

---

## Rooting Android with Magisk

> - [Here's how you can unlock the bootloader and root the Google Pixel 6a with Magisk](https://www.xda-developers.com/how-to-unlock-bootloader-root-magisk-google-pixel-6a/)
> - [How to download Android 13 ](https://www.xda-developers.com/how-to-download-android-13/)

### Downloads

Before we begin, read the device OEM unlocking guide and make sure to download the necessary files:

1. **Magisk App** - Download the latest Magisk app version from the official repository on GitHub - [Magisk Latest Release](https://github.com/topjohnwu/Magisk/releases/latest)

2. **Factory Image for Your Device** - Download the factory image for your device, **based on the installed release**. You can find guidance on downloading Android 13 factory images here: [XDA Developers - How to Download Android 13 Factory Image](https://www.xda-developers.com/how-to-download-android-13/) 
   - **Be sure to select the `Unlocked` link for the installed version in the Factory column!**


### Enable USB Debugging

- Throughout this process, you may need to enable `USB debugging` mode on your device when needed, because this option will be disabled when the device is unlocked or new images are flashed.

### Installation

1. **Installing Magisk on Your Android Device**:

   - Connect your Android device to your computer.

   - Use the following `adb` command to install the Magisk APK on your device:

```bash
# Download magisk.apk latest version from GitHub
adb install magisk.apk
```

2. **Preparing the Boot Image**:
   - Extract the downloaded factory image on your computer.
   - Inside the extracted files, locate another zip file and extract it as well.
   - Look for the `boot.img` file within these extracted files.

3. **Copying Boot Image to Your Android Device**:

   - Open a Terminal into the `boot.img` folder

   - Use the following `adb` command to copy the `boot.img` file to your Android device with Magisk installed:

```bash
adb push boot.img /sdcard/Download/
```

4. **Patching the Boot Image**:

   - Open the Magisk app on your Android device.
   - In the app, tap `Install`, `Select and Patch a file` and proceed with patching the `boot.img` file you copied earlier in the `/sdcard/Download/` directory.

5. **Copying the Patched File Back to Your Computer**:

   - After patching, use the following `adb` command to copy the patched file from your device back to your computer:

```bash
adb pull /sdcard/Download/magisk_[random].img
```

### Rooting the Device

1. **Prepare Your Unlocked Device**:
   - Ensure that your Android device is unlocked.
   - Turn off the device.
2. **Boot into Fastboot Mode**:
   - Turn the device back on by holding down both the volume down and power buttons simultaneously. This should boot your device into fastboot mode.
   - or use the `adb` commands

```bash
adb reboot bootloader
```

3. **Connect to the Computer**:
   - Connect your Android device to your computer via USB.

4. **Boot Magisk Image**:

   - In the command prompt or terminal on your computer, run the following command:

```bash
fastboot flash boot magisk_[random].img

fastboot reboot
```

- Your device should now be booted with Magisk.

5. **Enable USB Debugging as Needed**:
   - Enter the **System** > `Developer options` screen
     - `OEM Unlocking` should be **Enabled and greyed out**
     - **Turn off** `Automatic system updates`
     - **Enable** `USB debugging`, `Wireless debugging`, `Disable adb authorization timeout`

6. **Confirm Root Access**:

   - After rebooting, you can confirm that your device is rooted by using the following ADB command:

```bash
adb shell
su
```

7. Complete device setup by connecting to WiFi.

---

## Magisk Modules

> **Notes**
>
> - The `Installed` status shows the version of patch in the boot image. If this says N/A, you do not have root access - the boot image is not patched, or you have a problem with Magisk.
> - `App` simply shows the version of the app itself.

- Module Download & Zip from Github - `e.g` - MagiskSSH

### SSH for Magisk

> 🔗 [SSH by D4rCM4rC](https://github.com/Magisk-Modules-Repo/ssh)

```bash
mkdir ~/magisk_modules/
cd ~/magisk_modules/
wget https://github.com/Magisk-Modules-Repo/ssh/archive/refs/heads/master.zip -O magiskSSH.zip
unzip magiskSSH.zip -d magiskSSH
cd magiskSSH/ssh-master
zip -r magiskSSH.zip .
mv magiskSSH.zip ~/magisk_modules/
cd ~/magisk_modules/
rm -rf magiskSSH/

adb push magiskSSH.zip /sdcard/
adb shell
su
magisk --install-module /sdcard/magiskSSH.zip
```

- Put SSH keys into `/data/ssh/root/.ssh/authorized_keys` and `/data/ssh/shell/.ssh/authorized_keys`

```bash
echo "YOUR_SSH_PUB_KEY" > /data/ssh/root/.ssh/authorized_keys
chmod 700 /data/ssh/root/.ssh
chmod 600 /data/ssh/root/.ssh/authorized_keys
chown -R root:root /data/ssh/root/.ssh/
```

```bash
ssh root@<DEVICE_IP> -i ~/.ssh/"YOUR_SSH_PRIVATE_KEY"
# does NOT work - TO FIX
```

### [MagiskTrustUserCerts](https://github.com/NVISOsecurity/MagiskTrustUserCerts)

> This module makes all installed user certificates part of the system certificate store, so that they will automatically be used when building the trust chain. This module makes it unnecessary to add the  `network_security_config` property to an application's manifest.

```bash
wget https://github.com/NVISOsecurity/MagiskTrustUserCerts/releases/download/v0.4.1/AlwaysTrustUserCerts.zip
adb push AlwaysTrustUserCerts.zip /sdcard/
adb shell
su
magisk --install-module /sdcard/AlwaysTrustUserCerts.zip
```

-------^^^^^^^--------NOT WORKING-------^^^^^^^--------



- script

```bash
#!/system/bin/sh
# Please don't hardcode /magisk/modname/... ; instead, please use $MODDIR/...
# This will make your scripts compatible even if Magisk change its mount point in the future
MODDIR=${0%/*}

mkdir -p $MODDIR/system/etc/security/cacerts
rm $MODDIR/system/etc/security/cacerts/*
cp -f /data/misc/user/0/cacerts-added/* $MODDIR/system/etc/security/cacerts/
set_perm_recursive $MODDIR/system/etc/security/cacerts/ root root 644

# This script will be executed in post-fs-data mode
# More info in the main Magisk thread
```





### Magisk Movecert (WORKS)

-	Magisk - automatic "movecert" (to System CAs)
  + Dw zip and install 
    [https://github.com/Magisk-Modules-Repo/movecert/issues/12](https://github.com/Magisk-Modules-Repo/movecert/issues/12)

```bash
adb push movecert-1.9.zip /sdcard/
adb shell
su
magisk --install-module /sdcard/movecert-1.9.zip
```

-	 [movecert script](https://github.com/Magisk-Modules-Repo/movecert/blob/master/common/post-fs-data.sh)

```bash
#!/system/bin/sh
# Do NOT assume where your module will be located.
# ALWAYS use $MODDIR if you need to know where this script
# and module is placed.
# This will make sure your module will still work
# if Magisk change its mount point in the future
MODDIR=${0%/*}

# This script will be executed in post-fs-data mode

# If you for some reason do not want all your certificates moved from the user store to the system store, you can specify which certificates to move by replacing the * with the name of the certificate; i.e.,

# mv -f /data/misc/user/0/cacerts-added/12abc345.0 $MODDIR/system/etc/security/cacerts

mv -f /data/misc/user/0/cacerts-added/* $MODDIR/system/etc/security/cacerts
chown -R 0:0 $MODDIR/system/etc/security/cacerts

[ "$(getenforce)" = "Enforcing" ] || exit 0

default_selinux_context=u:object_r:system_file:s0
selinux_context=$(ls -Zd /system/etc/security/cacerts | awk '{print $1}')

if [ -n "$selinux_context" ] && [ "$selinux_context" != "?" ]; then
    chcon -R $selinux_context $MODDIR/system/etc/security/cacerts
else
    chcon -R $default_selinux_context $MODDIR/system/etc/security/cacerts
fi
```

```bash
find / -type f -name ***.0 2>/dev/null
find / -type d -name movecert 2>/dev/null
/data/adb/modules/movecert
/sbin/.magisk/modules/movecert
/sbin/.magisk/preinit/movecert

# CERTS FOLDERS
# - Mounted on the same volume (not symlinked but similar)

/data/adb/modules/movecert/system/etc/security/cacerts/

/sbin/.magisk/modules/movecert/system/etc/security/cacerts/

/sbin/.magisk/preinit/movecert/system/etc/security/cacerts/
```

```bash
ls -lah /data/adb/modules/movecert/system/etc/security/cacerts/
```



-	Other movecert module script
-	[MagiskTrustUserCerts](https://github.com/NVISOsecurity/MagiskTrustUserCerts/blob/master/post-fs-data.sh)

```bash
#!/system/bin/sh
# Please don't hardcode /magisk/modname/... ; instead, please use $MODDIR/...
# This will make your scripts compatible even if Magisk change its mount point in the future
MODDIR=${0%/*}

mkdir -p $MODDIR/system/etc/security/cacerts
rm $MODDIR/system/etc/security/cacerts/*
cp -f /data/misc/user/0/cacerts-added/* $MODDIR/system/etc/security/cacerts/
set_perm_recursive $MODDIR/system/etc/security/cacerts/ root root 644

# This script will be executed in post-fs-data mode
# More info in the main Magisk thread
```


### Magisk Frida Server

- MagiskFrida - Install
  - Dw zip from [https://github.com/ViRb3/magisk-frida/releases](https://github.com/ViRb3/magisk-frida/releases)

```bash
adb push MagiskFrida-*.zip /sdcard/
adb shell
su
ls
# Check if other Magisk version .zip are present and delete them

magisk --install-module /sdcard/MagiskFrida-*.zip
reboot
```



---

