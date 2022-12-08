# LinkedIn Learning

## Training Material

> Focused only on Android content only

* [Ethical Hacking: Mobile Devices and Platforms - Malcolm Shore](https://www.linkedin.com/learning/ethical-hacking-mobile-devices-and-platforms)
* [Android App Penetration Testing - Prashant Pandey](https://www.linkedin.com/learning/android-app-penetration-testing)



## Mobile Technology

## Android

### Understanding the Android OS

#### Android

* Based on Linux
* Many older versions are still in use
* Manufacturers introduce proprietary extensions
* Builds on the Linux security model

#### Android Kernel

* Does not run Linux applications
* Dalvik virtual machine evolves to Android Runtime AOT
* Linux-like shell without tools
* BusyBox extends ToolBox with Linux-like commands
  * Android 6 onwards, ToolBox has been replaced by ToyBox

#### Android Application

* Deployed as an APK package
  * Can change to a zip file and check its contents
* Executes as an application or service
* Helpers are broadcast receiver or content provider

#### Android Intents

* Manage requests
  * To start an activity
  * To access a broadcast receiver
  * To start a service
* Register to listen
* Define the listener or let the user choose
* Export activities as application-application intents

### Android Security Model

#### Android Security Model

* The trust boundary is the application
* Applications run in their own sandbox
* Interactions are explicit
* Applications are signed

#### Sourcing Android Applications

* The primary source is Google Play
* Third-party sources such as vendors
* Applications must have a valid signature
  * Self-signed
  * Authority-signed
* No certificate chain validation

#### Sandboxing Applications

* The application has a unique identifier
* Sandbox
  * The sandbox cannot interfere with other applications
  * Other applications cannot interfere with it
* Files stored in the sandbox
* Content provider for shared files
* Keystores with user passwords

#### Android Permissions

* Explicitly request permissions
* Minimize permissions requested
* Standard permissions, but applications can define their own
* Protection levels: normal or dangerous
* Permissions declared in the manifest file

#### Android Intents

* A message requesting an action, optionally with data
* Can be restricted with permissions
* Components specify the intents they can process
* Intent usage
  * startActivity
  * startService
  * sendBroadcast
* Explicit or implicit

### Preparing for Android Testing

#### 2 Environments to Install

* Java SDK
* Android SDK (or Android Studio)

### Extracting and Reading the Manifest File

#### Tools

* Apktool
* apps.evozi.com/apk-downloader

#### Apktool

> Search for app in Google play store, select an app and then its URL, and place it into the apps.evozi webpage to generate a download link then download

```bash
apktool d sms.apk
cd sms ; ls
    AndroidManifext.xml
    apktool.yml
    original
    res
    smali
cat apktool.yml  # Apk package info and Apktool info
cat AndroidManifest.xml  # Android permissions, activities, etc.
```

### Extracting and reading JavaScript Code

#### Tools

* dex2jar
* JD-GUI

```bash
d2j-dex2jar sms.apk  # Decompile the application
jd-gui  # Open sms-dex2jar.jar file in the GUI
```

### Recreating Java Source Code with JAD

```bash
# Extract class files after convertin the apk file to a zip
jad -d smsjava -s java smsreader/*.*  # Create new directory and convert class to java
cd smsjava ; ls
```

### Browsing Applications Directly with JADX

```bash
# Open the GUI and open an apk file within it
```

### Getting Command-line Access with ADB

## Testing

[https://mas.owasp.org/](https://mas.owasp.org/)

### Preparation

* Understand the application
  * Application functionality
  * Application Workflow
  * Supporting structure
* Things to prepare for
  * Using the application
  * Monitoring the traffic
  * Testing and demonstrating flaws

### Static Application Security Testing

* Application source code
* Reverse engineering
* Duplicate the runtime environment
* Identify all network interfaces
* Identify networking protocols
* Component interactions
* Data access
* Application interactions
* Server-side environment
  * Hosting provider
  * Authentication services
  * Payment gateway services
* Development environment
* Review permissions
* Hard-coded secret
* Security model
  * Access controls
  * Sanitization of inbound data
  * User authentication
  * Authorizations
  * Others such as licensing checks and tamper detection
* Session management
  * Proper session close
  * Misuse of session IDs
* Data sensitivity
* Data protection
  * Cloud storage
  * SD cards
  * Logs
  * Cache
* Encryption
  * Random number generators
  * Certificate management
* Pinning
  * Certificate
  * Public key
* OWASP Pinning Cheat Sheet

### Dynamic Application Security Testing

* Effect of funning the application
* Changes to the device storage
  * Sensitive data available
* Information transmitted
  * Unexpected connections
  * Sensitive application data being visible in transmissions
  * Sensitive identification data being transmitted
  * Wi-Fi and NFC protocols
* Subverting credential entry
* Web application testing
* Edge case testing
* Session management checks
* Good knowledge of the application
* Low-level testing
* Application hooking
* Disassembling

### Mobile Web Testing

#### Proxy Web Traffic (Android)

* Long press network name --> Modify network
* Show advanced options --> Proxy Manual
* Set the proxy address to the IP address of our server hosting Burp Suite
* Set the port to 8080

### Forensic Analysis

## Mobile OWASP Top 10 (2016)

[https://owasp.org/www-project-mobile-top-10/](https://owasp.org/www-project-mobile-top-10/)

* [M1: Improper Platform Usage](https://owasp.org/www-project-mobile-top-10/2016-risks/m1-improper-platform-usage)
* [M2: Insecure Data Storage](https://owasp.org/www-project-mobile-top-10/2016-risks/m2-insecure-data-storage)
* [M3: Insecure Communication](https://owasp.org/www-project-mobile-top-10/2016-risks/m3-insecure-communication)
* [M4: Insecure Authentication](https://owasp.org/www-project-mobile-top-10/2016-risks/m4-insecure-authentication)
* [M5: Insufficient Cryptography](https://owasp.org/www-project-mobile-top-10/2016-risks/m5-insufficient-cryptography)
* [M6: Insecure Authorization](https://owasp.org/www-project-mobile-top-10/2016-risks/m6-insecure-authorization)
* [M7: Client Code Quality](https://owasp.org/www-project-mobile-top-10/2016-risks/m7-client-code-quality)
* [M8: Code Tampering](https://owasp.org/www-project-mobile-top-10/2016-risks/m8-code-tampering)
* [M9: Reverse Engineering](https://owasp.org/www-project-mobile-top-10/2016-risks/m9-reverse-engineering)
* [M10: Extraneous Functionality](https://owasp.org/www-project-mobile-top-10/2016-risks/m10-extraneous-functionality)



