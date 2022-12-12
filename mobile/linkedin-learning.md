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

Configure device to Developer Mode

* Settings --> About --> select Build Entry 7 times --> Developer Options
  * Easier way to run `adb` is over USB. In Developer options select USB Debugging

```bash
adb devices  # Check what devices can be seen
adb push/pull  # Transfer files in/out of device
adb forward
adb install/uninstall
adb shell  # Create shell on Android device
```

### Install an Android Emulator from the SDK

```bash
SDK Manager  # Select the Android ADK package version(s)
android list targets
android list avd
emulator @Emu
adb devices
# Can now see emulated device
```

### Dynamic Analysis of Android Applications

```bash
adb install slack.apk  # Install app on device
adb shell
pm list packages  # List installed packages
ls /data/data
ls /data/data/com.Slack
exit
adb logcat
adb logcat > slacklog.txt
# Open up the Slack application and use it
adb shell
ls /data/data/com.Slack
ls -all /data/data/com.Slack/cache
ls -all /data/data/com.Slack/cache/image_manager_disk_cache
ls /data/data/com.Slack/databases
ls /data/data/com.Slack/files
exit
adb pull /data/data/com.Slack/databases/<FILE(s)>
# Download SQLite viewer for viewing database files (SQLite Browser)
# Change the file extensions to include .db at the end
# Enumerate the database
cat slacklog.txt | grep -i slack  # Review log file created earlier
```

### Using Drozer to Analyze Applications

```bash
adb devices
adb install agent.apk  # Install Drozer agent (from Drozer zip file)
adb forward tcp:31415 tcp:31415  # Forward TCP ports to enable connection; 
# the port to the Drozer server which the agent runs on the device
# Start the agent, then the app, then turn on the server
drozer console devices
drozer console connect
dz> list  # List modules that are available
dz> run app.package.list  # List packages on the device
dz> run app.package.info -a com.Slack  # Check package information
dz> run app.package.attacksurface com.Slack  # Check which areas can potentially be exploited
dz> run app.activity.info -a com.Slack
dz> run app.activity .start --component com.Slack com.Slack.ui.DeepLinkActivity
dz> run app.service.info -a com.Slack
dz> run app.service.send com.Slack
```

### Checking for Scam Phones

```bash
adb devices
adb shell
getprop  # Get phone properties
df  # Check storage
cat /proc/meminfo  # Check RAM
cat /proc/cpuinfo  # Check CPU
```

## Security Shepherd

### Taking a Look at Security Shepherd

OWASP Security Shepherd - [https://owasp.org/www-project-security-shepherd/](https://owasp.org/www-project-security-shepherd/)

### Watching for Bad Cryptography

### Insecure Data When in Developer Mode

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

## Android App Penetration Testing

### Android Application Components

#### Activity and Services

Activity:

* UI components of an app
* One activity = Screen interface + User action
* Webpage in web apps = Activity in mobile apps
* Interlinked through app flow or user interaction

Services:

* Services is a component without a UI
* Used to perform background activities
* Dependent on some UI component for start, such as music player, fitness tracker, location services

#### Content Providers and Receivers

Content Providers:

* Mechanism for information transfer between app
* Content providers
* Content resolvers
* Have to be specially protected

Broadcast Receivers:

* Listen for broadcast message from other applications
* System notifications are also received
* Broadcast receivers are triggered by events
* Intra-app broadcasts can also be received

### Aspects of Android Security

#### Web vs. Android Security

Web Application Security:

* Client-side testing insignificant
* Source code is not knjown
* Endpoint is out of scope
* App access is not a problem

Android Application Security:

* Client-side testing significant
* Source code is available
* Compromised endpoint is a risk
* APK distribution is a major problem

#### Domains of Android Security

1. Code Security
   1. Web check the client-side code of Android app
      1. Quality of the code, hardcoded IP addresses, credentials, weak cryptographic libraries, insecure certificates, or any other vulnerabilities associated with poor coding practices
2. Communication Security
   1. We check the interaction of the Android app with the server
      1. Test for vulnerabilities in the authentication, authorization, session management, and other dynamic parameters used in the interaction between the application and the server; similar to what we do with Burp Suite for web applications
3. Platform Interaction
   1. We test the interaction of the app with the device
      1. Check for leakages of sensitive information such as usernames, passwords, or other confidential data

#### Common Terminologies

* Automated testing
* Manual testing
* White-box testing
* Black-box testing
* DAST: dynamic application security testing
* SAST: static application security testing
* IAST: interactive application security testing

#### Lab Setup

...

### Static Application Testing

#### Introduction to MobSF

* Mobile Security Framework is an open-source tool
  * Static analysis, dynamic analysis, malware analysis,  and API testing of the APK file
* Performs a details static code analysis
* Components, certificates, manifest, and code analysis
* Requires Python 3.6 or above

#### Setting Up MobSF

[https://github.com/MobSF/Mobile-Security-Framework-MobSF](https://github.com/MobSF/Mobile-Security-Framework-MobSF)

Pre-reqs:

* JDK 8 or above
* Python 3.7 or above
* Git
* Microsoft Visual Studio 2019 (CE)

Installation:

```bash
git clone https://github.com/MobSF/Mobile-Security-Framework-MobSF.git
python3 -m pip install --upgrade pip
cd Mobile-Security-Framework-MobSF
setup.bat
```

#### Scanning Target Applications

```bash
# From Mobile-Security-Framework-MobSF directory
run.bat
# This will start a web server which we can navigate to locally
```

Search for a test application. Ex: Sieve Drozer (Intentionally vulnerable password manager)

Download and then drag and drop the APK file into the MobSF interface.

#### Manifest Analysis

Can be accessed from the left side pane of MobSF by selecting Security Analysis --> Manifest Analysis.

#### Code Analysis

Can be accessed from the left side pane of MobSF by selecting Security Analysis --> Code Analysis.

Words of Caution:

* Eliminate false positives
* Address the need for manual testing as well
* Third-party integrations
* Not everything can be fixed

### Dynamic Application Testing, Part 1

#### Introduction to Burp Suite

* Used to check security over network communication
* Creates a proxy netween the app and the server
* Uses MITM to intercept, monitor, modify, and retransmit the traffic
* Vulnerabilities like SQL injections, authentication flaws, etc.

#### Burp Suite Setup on Workstation

* Download and install Burp Suite
* Create proxy
  * Proxy --> Options --> Proxy Listeners  --> Uncheck Loopback Interface --> Add: Port = 5656, Interface = All or Current IP
* Download target application
  * [https://github.com/dineshshetty/Android-InsecureBankv2](https://github.com/dineshshetty/Android-InsecureBankv2)
    * `pip install -r requirements.txt`, `python3 app.py`
* Starting application server

#### Burp Suite Setup on Test Device

* Configure device proxy
  * Direct proxy setup over Wi-Fi
  * Using third-party proxy/VPN application
* Install certificates on device.

From Device:

1. Settings --> Wi-Fi settings --> Long press over Wi-Fi network -->  Modify Network --> Select Advanced Options --> Set proxy from None to Manual --> Enter in same proxy hostname and port in which the Burp Proxy is working. Ex: Current IP and port 5656
2. Test Configuration:
   1. Select browser on Android device --> open the link http://burp and look for Burp Suite configuration page
3. Install CA Certificate
   1. From the configuration page in the previous step, select the CA Certificate button in the top right corner, go to File Manager --> Downloads (may need to rename from cacert.der to cacert.cer) --> select the file and enter in a name and install for VPN and apps.

#### Application Testing: Brute Force

```bash
adb install InsecureBankv2.apk  # Install test APK onto emulator device
```

Open up the Emulator with the application and the Burp Suite interface turning the Intercept on.

From the application:

1. Set the host credentials
2. Test a login request
3. Send the intercepted request to Intruder
4. Select Sniper mode
   1. Add username and/or password payload position, select wordlist(s), start attack.

#### Application Testing: Password Change

> After logging into the application in the previous Brute Force stage

1. Select Change Password button
2. Enter in new password
3. Select Change Password
4. Send to Repeater
   1. Can make any changes to the password parameter as there are no authentication and authorization headers
   2. Could also potentially change for other known users by modifying the username parameter

Can browse the root folder of the application and view the Walkthroughs folder.

### Platform Interaction Testing

#### Introduction to Android Debug Bridge

#### Basic adb Commands

#### Testing Platforms: Insecure Logging

#### Testing Platforms: Insecure Data Storage

### Dynamic Application Testing, Part 2

#### Introduction to Drozer

#### Drozer Architecture

#### Drozer Setup

#### Sieve Application Overview

#### Basic Commands

#### Activity Testing

#### Content Provider Testing

#### Content Provider Testing: SQL Injection

