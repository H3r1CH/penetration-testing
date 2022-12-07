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

### Access ADB Shell from a VM/Networked Device

### Additional Emulator Options

### Physical Device Setup

### Common Issue: No Extended Controls

## Android Static Analysis

## Android Dynamic Analysis

## Android Bug Bounty Hunt

## BONUS - Android Red Teaming



