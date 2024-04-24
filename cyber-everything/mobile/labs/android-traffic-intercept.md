# Intercepting Android App Traffic

I've used my KaliVM and this instructions to follow IppSec's video and get everything working as he described.

> 🔗 [Intercepting Android App Traffic with BurpSuite](https://www.youtube.com/watch?v=xp8ufidc514) - by [IppSec](https://ippsec.rocks/)
>
> #### Video Timeline
>
> ```
> 00:00 - Introduction, talking about RouterSpace and why we can't just do what we did in that video
> 01:25 - Installing Genymotion, Virtual Box, and ADB; while talking about why I don't use Android Studio/AVD. Simply because genymotion just works.
> 02:05 - Make sure you upgrade your memory, processors, and enable Virtualization in your VM Settings!
> 02:30 - Running Genymotion and starting a Pixel 3 XL
> 03:37 - Converting BurpSuites Certificate to PEM Format with openssl x509 -inform der -in [name of cert] -out burp.pem
> 04:20 - Renaming the certificate to 9a5ba575.0, and showing how we got that name
> 06:00 - Starting the device and showing the certificate authorities
> 07:00 - Copying the certificate to /system/etc/security/cacerts/, and showing how to remount to rw
> 08:10 - Showing how to set the proxy through both the GUI and via ADB
> 09:50 - Installing GAPPS
> 10:30 - Showing how to unset the proxy from ADB
> 11:00 - Creating an alias to set and unset the proxy via adb
> 12:00 - Opening the google play store and logging in and install Wayzn to see if we can intercept traffic
> 15:20 - Showing we intercepted traffic from Wayzn, then installing Instagram
> 16:50 - Attempting to login to instagram and getting an error message
> 17:20 - Setting up Frida both on our computer and android device
> 19:20 - Showing Frida is working, getting ps output from the android device
> 19:55 - Downloading the instragram ssl pinning bypass script
> 21:20 - Using frida to start instagram and loading the script to bypass the SSL Checking
> 22:15 - Setting the proxy and showing us intercept instagram traffic
> ```

> #### 🌐 Resources 🔗
>
> * [Genymotion](https://www.genymotion.com/download/)
> * [Frida](https://frida.re/)
> * [OWASP SSL Certificate and Public Key Pinning](https://owasp.org/www-community/controls/Certificate\_and\_Public\_Key\_Pinning)
> * Extra video - [Getting Started with Android App Testing with Genymotion - InsiderPhD](https://www.youtube.com/watch?v=\_HRpLPrlg1U)

```bash
sudo apt install -y virtualbox adb
sudo wget https://dl.genymotion.com/releases/genymotion-3.5.1/genymotion-3.5.1-linux_x64.bin -O /tmp/genymotion.bin

sudo chmod +x genymotion.bin
sudo ./genymotion.bin

cd /opt/genymobile/genymotion
./genymotion
```

* Install a new Google Pixel 3 XL device
* Run Burpsuite and copy its certificate

```bash
cd
curl localhost:8080/cert -o cert.der
sudo mv cert.der /usr/share/ca-certificates/BurpSuiteCA.der

openssl x509 -inform der -in /usr/share/ca-certificates/BurpSuiteCA.der -out burp.pem
openssl x509 -inform PEM -subject_hash_old -in burp.pem
mv burp.pem 9a5ba575.0 	# Output from above
```

* Copy cert to device

```bash
adb devices -l
adb shell
su
mount -o remount,rw /
exit
exit

adb push 9a5ba575.0 /system/etc/security/cacerts/
```

![](.gitbook/assets/2023-07-02\_17-21-49\_134.png)

* To start capturing traffic with BurpSuite, set the proxy listener to `All interfaces`

![](.gitbook/assets/2023-07-02\_17-25-13\_135.png)

* Set the proxy usage on the device, with the KaliVM IP

```bash
adb shell settings put global http_proxy 192.168.31.128:8080

# Create an alias for set and unset proxy
alias adb_set_proxy="adb shell settings put global http_proxy $(ip -o -4 addr show eth1 | awk '{print $4}' | sed 's/\/.*//g'):8080"

alias adb_unset_proxy="adb shell settings put global http_proxy :0"
```

* From Genymotion, click OpenGAPPS to install Gapps on the device and restart the device.

```bash
# Disable proxy
adb_unset_proxy
```

* Run Play Store on the device and install apps
  * `e.g.` - Wayzn, Instagram - in this case
* Try to set the proxy, turn BurpSuite intercept on and login into the _Wayzn_ app

```
adb_set_proxy
```

![](.gitbook/assets/2023-07-02\_17-46-30\_136.png)

* Open Instagram and try to login. `Unable to log in` with proxy set.

![](.gitbook/assets/2023-07-02\_17-49-59\_137.png)

* Unsetting the proxy, Instagram error changes.
  * Instagram prevents from intercepting the traffic

![](.gitbook/assets/2023-07-02\_17-50-46\_138.png)

* Install Frida
  * 🔗 Follow [Frida Android](https://frida.re/docs/android/)

```bash
# On KaliVM
pipx install frida-tools                                   

# On device
adb shell
uname -a
	Linux localhost 5.10.101-genymotion+-ab74 #1 SMP PREEMPT Thu Dec 1 14:03:02 UTC 2022 x86_64

# x86_64 -  Download frida-server-16.1.1-android-x86_64.xz
exit
wget https://github.com/frida/frida/releases/download/16.1.1/frida-server-16.1.1-android-x86_64.xz

7z x frida-server-16.1.1-android-x86_64.xz
mv frida-server-16.1.1-android-x86_64 frida-server

adb push frida-server /data/local/tmp/
adb shell "chmod 755 /data/local/tmp/frida-server"
adb shell "/data/local/tmp/frida-server &"
```

* Check **Frida** is working

```bash
frida-ps -U		# This is device output
     PID  Name
    ----  -------------------------------------------------------------
    5446  Google Play Store
    6085  Instagram
    1960  Phone
    5249  Wayzn
     473  adbd
    1268  android.ext.services
    [...]
```

* Download the [Instagram SSL Pinning Bypass](https://github.com/Eltion/Instagram-SSL-Pinning-Bypass) script 22:15 - Setting the proxy and showing us intercept instagram traffic

```
sudo mkdir -p /opt/android/instagram
cd /opt/android/instagram

sudo wget https://raw.githubusercontent.com/Eltion/Instagram-SSL-Pinning-Bypass/main/instagram-ssl-pinning-bypass.js
```

* Force stop Instagram app from App info
* Use Frida to start Instagram and load the script to bypass the SSL Checking

```bash
adb_set_proxy
frida -U -l ./instagram-ssl-pinning-bypass.js -f com.instagram.android
```

![](.gitbook/assets/2023-07-02\_18-07-35\_139.png)

* Show Instagram intercepted traffic in BurpSuite

![](.gitbook/assets/2023-07-02\_18-09-42\_140.png)

***
