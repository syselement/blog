# MASTG Techniques

> 📌 *These study notes serve as a concise reference, capturing the essential insights, guidelines, and best practices for securing mobile applications, based on the OWASP MASTG (**M**obile **A**pplication **S**ecurity **T**esting) Standard - Techniques.*

------

## 🌐 Resources 🔗

> - [OWASP MASTG](https://mas.owasp.org/MASTG/)
> - [OWASP MASVS](https://mas.owasp.org/MASVS/)
> - [MASTG - Tests](https://mas.owasp.org/MASTG/tests/)
> - [MASTG - Techniques](https://mas.owasp.org/MASTG/techniques/)
> - [MASTG - Tools](https://mas.owasp.org/MASTG/tools/)
> - [MASTG - Apps](https://mas.owasp.org/MASTG/apps/)

------

## Android

### Device Remote Shell

- Enable Developer Mode and USB debugging on the device

> [`adb`](https://developer.android.com/tools/adb) (Android Debug Bridge) - listen port `5037`

```bash
# USB
adb devices -l
adb shell
adb -s <device_ID> shell
su

# WiFi (same network)
# Set a listener on the device (via USB)
adb tcpip 5555
# Disconnect USB and run from host pc (same network)
adb connect <device_IP>
adb devices
adb shell

# SSH
# On device > Setup Termux (default port 8022)
sshd
# On Host pc
ssh -p 8022 <device_IP>

# "SSH for Magisk" Module can be used too
```

### **Data transfer**

- using Android Studio device file explorer
- using `adb`

```bash
adb pull <remote> <local>
adb push <local> <remote>
```

> [`objection`](https://github.com/sensepost/objection) - runtime mobile exploration toolkit for security testing on non-rooted devices. Injects the Frida gadget into an application by repackaging it, disable SSL pinning methods, access app storage, execute custom Frida scripts, list activities/services/broadcast receivers, start activities, ecc
>
> - On rooted device with frida-server configured, Objection can connect directly to it without app repackage.
> - Interact with it via Objection REPL
> - *The ability to **perform advanced dynamic analysis on non-jailbroken devices** is one of the features that makes Objection incredibly useful.*

- [using `objection`](https://github.com/sensepost/objection/wiki/Using-objection)

```bash
# Connect to a patched APK
objection explore

# Connect to a specific app
frida-ps -Ua | grep -i telegram
objection --gadget="org.telegram.messenger" explore
cd ..
ls
file download <file_name>
```

- using [Termux](https://wiki.termux.com/wiki/Remote_Access) remote access (e.g. SFTP)

```bash
sftp -P 8022 root@localhost
```

### Obtaining and Extracting Apps

- Non-official websites/third party stores like APKMirror, APKPure. Pay attention to the APK that may contain malware!!!
- Extract the App Package from the Device

```bash
adb shell pm list packages
adb shell pm list packages | grep -i <name>
adb shell pm list packages -3 -f
adb shell pm path <package name>
adb pull <apk path>
```

### Repack & Install App

- [Patching Android Apps](https://github.com/sensepost/objection/wiki/Patching-Android-Applications)

```bash
objection patchapk --source app-release.apk
```

- Install apps via `adb`

```bash
adb install <path_to_apk>
```

### App Information Gathering

#### [List installed Apps](https://mas.owasp.org/MASTG/techniques/android/MASTG-TECH-0006/)

```bash
adb shell pm list packages
adb shell pm list packages -3 -f
adb shell pm path <app_package_id>

# Get all installed apps on USB device & # List running apps
frida-ps -Uai
adb shell ps
```

#### [Exploring the app package](https://mas.owasp.org/MASTG/techniques/android/MASTG-TECH-0007/)

🔗 [Reference Training Apps](https://mas.owasp.org/MASTG/apps/)

> [`apktool`](https://github.com/iBotPeaches/Apktool)

```bash
# Unpack APK
apktool d <app-name.apk>
```

📌 The **Android Manifest** is the main source of information, includes package name, permissions, app components, etc.

```bash
cat apktool.yml

grep -i <keyword> AndroidManifest.xml

# e.g.
grep -i permission AndroidManifest.xml
grep -i android:allowBackup AndroidManifest.xml
grep -i activity AndroidManifest.xml
grep -i service AndroidManifest.xml
grep -i provider AndroidManifest.xml
grep -i receiver AndroidManifest.xml
grep -i debuggable AndroidManifest.xml
```

#### [Accessing App Data Directories](https://mas.owasp.org/MASTG/techniques/android/MASTG-TECH-0008/)

```bash
frida-ps -Uai | grep -i appname
objection -g it.appname explore

env
    Name                    Path
    ----------------------  --------------------------------------------------------------------------------
    cacheDirectory          	  /data/user/0/it.appname/cache
    codeCacheDirectory        /data/user/0/it.appname/code_cache
    externalCacheDirectory   /storage/emulated/0/Android/data/it.appname/cache
    filesDirectory          		 /data/user/0/it.appname/files
    obbDir                 			    /storage/emulated/0/Android/obb/it.appname
    packageCodePath        	    /data/app/~~JEIPs_hOcZEloovI4ycnFw==/it.appname-JYsDjTAW2zm7oBXBiPT7xQ==/base.apk

ls
	Type       Last Modified            Read  Write  Hidden  Size       Name
	---------  -----------------------  ----  -----  ------  ---------  --------------------------------------------------------------------------------------------
	File       2022-09-14 08:03:22 GMT  True  True   False   0.0 B      generatefid.lock
	Directory  2023-08-04 13:05:27 GMT  True  True   False   3.4 KiB    datastore
	Directory  2023-07-19 08:30:32 GMT  True  True   False   3.4 KiB    config
	File       2022-09-20 11:35:03 GMT  True  True   False   44.6 KiB   didomi_config_cache.json
	Directory  2023-08-04 10:25:44 GMT  True  True   False   3.4 KiB    AFRequestCache
	File       2023-08-01 13:12:28 GMT  True  True   False   562.0 B    PersistedInstallation.W0RFRkFVTFRd+MTo1NjYwOTMxNjMwMjQ6YW5kcm9pZDozMTZhOTNiN2FkNjgzYWUz.json
	File       2023-08-04 13:03:53 GMT  True  True   False   31.0 B     optly-background-watchers.json
```

#### [Monitoring System Logs](https://mas.owasp.org/MASTG/techniques/android/MASTG-TECH-0009/)

> [`logcat`](https://developer.android.com/tools/logcat)

```bash
adb logcat > logcat.log

# Specific app log output
frida-ps -Uai | grep -i <name>
adb logcat | grep "$(adb shell ps | grep <package-name> | awk '{print $2}')"
```

### [Network Monitoring](https://mas.owasp.org/MASTG/techniques/android/MASTG-TECH-0010/)

> Tools:
>
> - `tcpdump`
> - `netcat`
> - `Wireshark`

- **Traffic Sniffing** - tcpdump / Wireshark

```bash
# On Android phone
tcpdump

# Remote sniff Android phone's network traffic
tcpdump -i wlan0 -s0 -w - | nc -l -p 11111

# On host, listen to traffic using Wireshark
adb forward tcp:11111 tcp:11111
nc localhost 11111 | wireshark -k -S -i -
```

### [Interception Proxies](https://mas.owasp.org/MASTG/techniques/android/MASTG-TECH-0011/)

> [`OWASP ZAP`](https://github.com/zaproxy/zaproxy)
>
> [`Burp Suite`](https://portswigger.net/burp/releases#community)
>
> - [Configuring an Android Device to Work With Burp](https://support.portswigger.net/customer/portal/articles/1841101-configuring-an-android-device-to-work-with-burp).
> - [Installing Burp's CA certificate to an Android device](https://support.portswigger.net/customer/portal/articles/1841102-installing-burp-s-ca-certificate-in-an-android-device).
> - [Configuring an iOS Device to Work With Burp](https://support.portswigger.net/customer/portal/articles/1841108-configuring-an-ios-device-to-work-with-burp).
> - [Installing Burp's CA certificate to an iOS device](https://support.portswigger.net/customer/portal/articles/1841109-installing-burp-s-ca-certificate-in-an-ios-device).

**BurpSuite**

- Export cert from BurpSuite in DER format (or download the cert from the `http://burpsuite` page after setting the Proxy on the device)

```bash
openssl x509 -inform DER -in burp-ad -out burp.pem
adb push burp.pem /sdcard/
# Install burp.pem in the user certificate store

# Move User Certificates to rot store / System CAs (using Magisk module Movecert)
```

- Set Proxy on device WiFi Network

  - Host with BurpSuite must be on the same WiFi network
  - [Bypass NET::ERR_CERT_VALIDITY_TOO_LONG](https://blog.nviso.eu/2018/01/31/using-a-custom-root-ca-with-burp-for-inspecting-android-n-traffic/)

 - [`"NET::ERR_CERTIFICATE_TRANSPARENCY_REQUIRED"`](https://httptoolkit.com/blog/chrome-android-certificate-transparency/) 

**ZapProxy**

🔗 [Setting up ZAP for Android](https://security.secure.force.com/security/tools/webapp/zapandroidsetup)

#### Bypassing the Network Security Configuration

- 🔗 [Adding the Proxy's certificate among system trusted CAs using Magisk](https://mas.owasp.org/MASTG/techniques/android/MASTG-TECH-0011/#adding-the-proxys-certificate-among-system-trusted-cas-using-magisk)
- 🔗 [MagiskTrustUserCerts](https://github.com/NVISOsecurity/MagiskTrustUserCerts)
  - This module will automatically add all user-installed CA certificates to the list of the system trusted CAs (reboot is necessary)


**Manual**

- *Not always working*

```bash
adb shell
mount -o rw,remount /system
# mount -o rw,remount -t ext4 /system

openssl x509 -inform DER -in cacert.der -out cacert.pem
openssl x509 -inform PEM -subject_hash_old -in cacert.pem | head -1
mv cacert.pem <hash>.0
mv <hash>.0 /system/etc/security/cacerts
chmod 644 <hash>.0
```

> 📌 Starting with Android 7.0 (API level 24), the Android OS will no longer  trust user CA certificates by default, unless specified in the  application

#### Obstacles

- Security controls such as **root detection**, **certificate pinning**
- [Client isolation](https://documentation.meraki.com/MR/Firewall_and_Traffic_Shaping/Wireless_Client_Isolation) in wireless networks

```bash
# Intercept traffic in WiFis with Client Isolation

# Configure Android device proxy to point to 127.0.0.1:8080
# Connect device via USB to host PC
# Make a reverse port forwarding
adb reverse tcp:8080 tcp:8080
```

- **Non-proxy-aware** apps: `e.g.` Xamarin, or the app verifies if proxy is set and doesn't allow any traffic if it is set

```bash
# Enable "Support invisible proxying" in Burp Proxy request handling options

# iptables - redirect Android traffic to interception proxy
iptables -t nat -A OUTPUT -p tcp --dport 80 -j DNAT --to-destination <Your-Proxy-IP>:8080

iptables -t nat -L
# Reset iptables
iptables -t nat -F

# bettercap - target and host on same network
sudo bettercap -eval "set arp.spoof.targets <ANDROID-IP>; arp.spoof on; set arp.spoof.internal true; set arp.spoof.fullduplex true;"
```

- Proxy detection - bypass using Frida script

### [Bypassing Certificate Pinning](https://mas.owasp.org/MASTG/techniques/android/MASTG-TECH-0012/)

***Static***

Intercepting certificate is not accepted as a valid certificate if the app is implementing SSL Pinning.

- **Frida**: Use the [frida-multiple-unpinning](https://codeshare.frida.re/@akabe1/frida-multiple-unpinning/) script

```bash
frida --codeshare akabe1/frida-multiple-unpinning -f YOUR_BINARY
```

- **Objection** (rooted device): Use the [android sslpinning disable](https://github.com/sensepost/objection/blob/master/objection/console/helpfiles/android.sslpinning.disable.txt) command

```bash
android sslpinning disable
```

- **Xposed**: Install the [TrustMeAlready](https://github.com/ViRb3/TrustMeAlready) or [SSLUnpinning](https://github.com/ac-pm/SSLUnpinning_Xposed) module.

**Custom Certificate Pinning**

- 🔗 [Advanced - Bypassing SSL Pinning in Android Applications](https://serializethoughts.wordpress.com/2016/08/18/bypassing-ssl-pinning-in-android-applications/)

```bash
# Certificate hashes
grep -ri "sha256\|sha1" ./smali
# Replace hashes with proxy's CA hash

# Certificate files
find ./assets -type f \( -iname \*.cer -o -iname \*.crt \)
find ./res -type f
# Replace files with proxy's certificates

# Truststore files
find ./ -type f \( -iname \*.jks -o -iname \*.bks \)
```

- `e.g.` App uses BKS ([BouncyCastle](https://github.com/sensepost/objection/blob/master/objection/console/helpfiles/android.sslpinning.disable.txt)) truststore

```bash
keytool -importcert -v -trustcacerts -file proxy.cer -alias aliascert -keystore "res/raw/truststore.bks" -provider org.bouncycastle.jce.provider.BouncyCastleProvider -providerpath "providerpath/bcprov-jdk15on-164.jar" -storetype BKS -storepass password

# proxy.cer - proxy's certificate
# aliascert - alias for proxy's cert
# BouncyCastle Provider jar file
# password - keystore pw, from decompiled app code

# List BKS truststore certs
keytool -list -keystore "res/raw/truststore.bks" -provider org.bouncycastle.jce.provider.BouncyCastleProvider -providerpath "providerpath/bcprov-jdk15on-164.jar"  -storetype BKS -storepass password

# Repackage the app and install it on device
```

***Dynamic***

More convenient and faster to perform.

Find the correct method to hook by searching for strings and licences files, identifying the used library. Examine the source code (SMALI code) to find methods suited for dynamic instrumentation.

- 🔗 [Circumventing SSL Pinning in obfuscated apps with OkHttp](https://blog.nviso.eu/2019/04/02/circumventing-ssl-pinning-in-obfuscated-apps-with-okhttp/)

```bash
# Search for all methods that take a string and a variable list of strings as args and return a complex object

grep -ri java/lang/String;\[Ljava/lang/String;)L ./
```

- Hook each method with Frida and print the arguments. Find the one that prints out a domain name and a cert hash. Modify the arguments to circumvent the implemented pinning.

### [Reverse Engineering Android Apps](https://mas.owasp.org/MASTG/techniques/android/MASTG-TECH-0013/)

[...]

---

