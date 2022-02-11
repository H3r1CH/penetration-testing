# Support and Tools

## Notes

### OS Versions

* Android 6 - Device does not need to be rooted
* Android 7.1 - Device needs to be rooted before it will accept the burp cert

### Certificate Pinning

## Setup

### Frida Server

Find out the arch version of the device

`adb shell getprop ro.product.cpu.abi`

Download the Frida server version from the following URL and extract it

* https://github.com/frida/frida/releases/
* frida-server-xxxx-android-x86.xz
* frida-server-xxxx-android-x86\_64.xz
* Windows: `adb push C:\ADB\<frida-server-name> /data/local/tmp`
* Linux: `adb push ADB/<frida-server-name> /data/local/tmp`
* Both: `adb shell chmod 777 /data/local/tmp/<frida-server-name>`

### Burp

1. Open Burp and go to Proxy tab
2. Select Options tab
3. Click the Import/Export CA Certificate
4. Export Certificate in DER format
5. Push the Certificate onto the device with ADB (We will need this later for Frida)
   1. `adb push cacert.der /data/local/tmp/cert-der.crt`

### SSL Pinning

Script that will be used to overwrite the certificate pinning mechanism

[https://codeshare.frida.re/@pcipolloni/universal-android-ssl-pinning-bypass-with-frida/](https://codeshare.frida.re/@pcipolloni/universal-android-ssl-pinning-bypass-with-frida/)

#### Script injection to bypass certificate pinning

* Push the script onto the device
  * `adb push C:\ADB\fridascriptjs /data/local/tmp`
* Check and run Frida server on the device
  * `adb shell /data/local/tmp/<frida-server-name> &`
* List all the running processes on the device
  * `frida-ps -U`
* Locate your applications package name from the list
  * i.e. 3672 com.twitter.android
* Hook Frida script into your application
  * `frida -U -f com.twitter.android -l <path-to-fridascript.js> --no-paus`

#### Why we need certificate pinning bypass

* Mobile apps encrypt traffic for HTTPS which uses a certificate to do this
* We can insert Burp certificate into Android to capture requests
* Developer protection: Certificate pinning
  * App checks to make sure only it's own cert can encrypt traffic
* Our Solution: Certificate pinning bypass
  * Overwrite the code in the API that's responsible for check cert
  * Frida

## Tools

* ADB (Android Debug Bridge)
* jadx
* dex2jar
* Frida
* Burpsuite
* Genymotion
* Android Studio Virtual Device Manager
