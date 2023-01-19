---
description: 'Resource: https://tryhackme.com/room/androidhacking101'
---

# Methodology

## Method

1. Information Gathering
2. Reversing
3. Static Analysis
4. Dynamic Analysis
5. Report

## Information Gathering

How do I find the application of the organization?

Easy, play store: is a digital distribution platform for mobile apps for devices with Android operating system.

`https://play.google.com/store/apps/details?id=com.shortstack.hackertracker`

> Where `id=` specifies the name of the package for a select app

## Reversing

### View Devices

#### ADB

Android Debug Bridge (ADB) is a development tool that facilitates communication between an Android device and a personal computer.

* `adb devices`

### Extract APK

* You will need to have installed the app on your device and know package name
* `adb shell pm path package_name`
* `adb shell pm path com.shortstack.hackertracker`
* This will print the path to the APK of the given
* `package /data/app/com/shortstack.hackertracker-1/base.apk`
* The below command pulls the file remote to local. If local isn't specified, it will pull to the current folder.
* `adb pull <remote> [<localDestination>]`
* `adb pull /data/app/com.shortstack.hackertracker-1/base.apk`

### Get the Source Code

#### jadx

The jadx suite allows you to simply load an APK and look at its Java source code. What’s happening under the hood is that jADX is decompiling the APK to smali and then converting the smali back to Java.

* `jadx -d [path-output-folder] [path-apk-or-dex-file]`

#### Dex2Jar

Use dex2jar to convert an APK to a JAR file.

* `d2j-dex2jar.sh or .bat /path/application.apk`
* Once you have the JAR file, simply open it with JD-GUI and you’ll see its Java code.

#### apktool

This tool get a source code in smali.

* `apktool d file.apk`

#### jadx-gui

UI version of jadx

* `jadx\bin\jadx-gui`

## Static Analysis

### Vulnerabilities

* Weak or improper cryptography use
* Exported Preference Activities
* Apps which enable backups
* Apps which are debuggable
* App Permissions.
* Firebase Instance(s)
* Sensitive data in the code

#### Weak or improper cryptography use

Incorrect uses of encryption algorithm may result in sensitive data exposure, key leakage, broken authentication, insecure session and spoofing attack.

Example: For Java implementation, the following API is related to encryption. Review the parameters of the encryption implementation.

* `IvParameterSpec iv = new IvParameterSpec(initVector.getBytes("UTF-8"));`
* `SecretKeySpec skeySpec = new SecretKeySpec(key.getBytes("UTF-8"), "AES");`

How to search this when I have the source code of the application?

* `grep -r "SecretKeySpec" *`
* `grep -rli "aes" *`
* `grep -rli "iv"`

Solution: Use a cryptography asymmetric.

#### Exported Preference Activities

As we know, Android's activity component is application screen(s) and the action(s) that applied on that screen(s) when we use the application. When as activity is found to be shared with other apps on the device therefore leaving it accessible to any other application on the device.

Okay, exploit this in dynamic analysis... How identify the activity is exported?

With your favorite editor of text. Gedit/Vim/subl, etc… open the AndroidManifest.xml or use cat and grep.

`cat AndroidManifest.xml | grep "activity" --color`

#### Apps which enable backups

This is considered a security issue because people could backup your app via ADB and then get private data of your app into their PC.

1. Shared preference.
2. directory returned by getFilesDir().
3. getDataBase(path) also includes files created by SQLiteOpenHelper.
4. files in directories created with getDir(Sring, int).
5. files on external storage returned by getExternalFilesDir (String type).

How to identify this?

Open the AndroidManifest.xml or use cat & grep: android:allowBackup="true"

`cat AndroidManifest.xml | grep "android:allowBackup" --color`

Solution: android:allowBackup="false"

#### Apps which are debuggable

Debugging was enabled on the app which makes it easier for reverse engineers to hook a debugger to it. This allows dumping a stack trace and accessing debugging helper classes.

How to identify this? Open the AndroidManifest.xml or use cat & grep: android:debuggable="true"

`cat AndroidManifest.xml | grep "android:debuggable" --color`

...

#### Firebase Instance(s)

Last year, security researchers have discovered unprotected Firebase databases of thousands of iOS and Android mobile applications that are exposing over 100 million data records, including plain text passwords, user IDs, location, and in some cases, financial financial records such as banking and cryptocurrency transactions.

Google's Firebase service is one of the most popular back-end development platforms for mobile and web applications that offers developers a cloud-based database, which stores data in JSON format and synced it in the real-time with all connected clients.

How identify this?

[FireBase Scanner](https://github.com/shivsahni/FireBaseScanner), The script helps security analysts to identify misconfigured firebase instances.

`git clone https://github.com/shivsahni/FireBaseScanner`

`python FireBaseScanner.py -p /path/apk`

#### Sensitive data in the code

Users, passwords, internal IP and more...

With your favorite editor of text, Gedit/Vim/subl, etc…, grep or GUI decompiler back to reversing and experiment with your favorite tool.

#### App Permissions

System permissions are divided into two groups: “normal” and “dangerous.” Normal permission groups are allowed by default, because they don’t pose a risk to your privacy. (e.g., Android allows apps to access the Internet without your permission.) Dangerous permission groups, however, can give apps access to things like your calling history, private messages, location, camera, microphone, and more. Therefore, Android will always ask you to approve dangerous permissions.

### Frameworks

#### MARA Framework: [https://github.com/xtiankisutsa/MARA\_Framework](https://github.com/xtiankisutsa/MARA\_Framework)

Is a Mobile Application Reverse engineering and Analysis Framework. It is a tool that puts together commonly used mobile application reverse engineering and analysis tools, to assist in testing mobile applications against the OWASP mobile security threats.

#### QARK: [https://github.com/linkedin/qark](https://github.com/linkedin/qark)

Is a static code analysis tool, designed to recognize potential security vulnerabilities and points of concern for Java-based Android applications.

#### MobSF: [https://mobsf.github.io/docs/#/](https://mobsf.github.io/docs/#/)

Is an automated, all-in-one mobile application (Android/iOS/Windows) pen-testing, malware analysis and security assessment framework capable of performing static and dynamic analysis.

### Complications

#### Obfuscate Code

Is the process of modifying an executable so that it is no longer useful to a hacker but remains fully functional. While the process may modify actual method instructions or metadata, it does not alter the output of the program. To be clear, with enough time and effort, almost all code can be reverse engineered. However, on some platforms (such as Java, Android, iOS and .NET) free decompilers can easily reverse-engineer source code from an executable or library in virtually no time and with no effort. Automated code obfuscation makes reverse-engineering a program difficult and economically unfeasible.

#### Proguard

To obfuscate the code, use the Proguard utility, which makes these actions:

* Removes unused variables, classes, methods, and attributes
* Eliminates unnecessary instructions
* Removes Tutorial information: obfuscate Androiddepuración code;
* Renames classes, fields, and methods with illegible names

#### DEXGUARD

The enhanced commercial version of Proguard. This tool is capable of implementing the text encryption technique and renaming classes and methods with non-ASCII symbols.

#### Deguard

It is based on powerful probabilistic graphical models learned from thousands of open source programs. Using these models, Deguard retrieves important information in Android APK, including method and class names, as well as third-party libraries. Deguard can reveal string decoders and classes that handle sensitive data in Android malware.

## Dynamic Analysis

### Getting Started

#### Installing Applications with ADB

`adb install apkfilename.apk`

#### Intercepting Traffic of the Application

#### Burp Suite

Is an integrated platform for performing security testing of web applications. Its various tools work seamlessly together to support the entire testing process, from initial mapping and analysis of an application’s attack surface, through to finding and exploiting security vulnerabilities.

Installing trusted CA at the Android OS level (Root device/Emulator) for Android N+ as the following:

* `openssl x509 -inform PEM -subject_hash -in BurpCA.pem | head -1`
* `cat BurpCA.pem > 9a5ba580.0`
* `openssl x509 -inform PEM -text -in BurpCA.pem -out /dev/null >> 9a5ba580.0`
* `adb root`
* `abd remount`
* `adb push 9a5ba580.0 /system/etc/security/cacerts/`
* `adb shell "chmod 644 /system/etc/security/cacerts/9a5ba580.0"`
* `adb shell "reboot"`

#### PID Cat

Tool for shows log entries for a specific application package when debug=true is enable in the app.

#### Drozer

drozer helps to provide confidence that Android apps and devices being developed by, or deployed across, your organisation do not pose an unacceptable level of risk. By allowing you to interact with the Dalvik VM, other apps’ IPC endpoints and the underlying OS.

drozer provides tools to help you use and share public exploits for Android. For remote exploits, it can generate shellcode to help you to deploy the drozer Agent as a remote administrator tool, with maximum leverage on the device.

drozer is a comprehensive security audit and attack framework for Android.

Basic example, Abusing unprotected activities:

* `adb forward tcp:31415 tcp:31415`
* `drozer console connect`
* Now download and install [apk](https://github.com/as0ler/Android-Examples/raw/master/sieve.apk) for this example

Retrieving package information:

* `run app.package.list -> see all the packages installed`
* `run app.package.info -a -> view package information.`

Identifying the attack surface -> activities unprotected and more....

* `run app.package.attacksurface package_name`

View what activities can be exploited.

* `run app.activity.info -f package_name`

Start activities unprotected!

* `run app.activity.start --component package name component_name`

Exploiting Content Provider

```bash
run app.provider.info -a package_name
run scanner.provider.finduris -a package_name
run app.provider.query uri
run app.provider.update uri --selection conditions selection_arg column data
run scanner.provider.sqltables -a package_name
run scanner.provider.injection -a package_name
run scanner.provider.traversal -a package_name
```

Exploiting Service

```bash
run app.service.info -a package_name
run app.service.start --action action --component package_name component_name
run app.service.send package_name component_name --msg what arg1 arg2 --extra type key value --bundle-as-obj
```

#### Inspeckage - Android Package Inspector

My favorite tool, Inspeckage is a tool developed to offer dynamic analysis of Android applications. By applying hooks to functions of the Android API, Inspeckage will help you understand what an Android application is doing at runtime. Inspeckage will let you interact with some elements of the app, such as activities and providers (even unexported ones), and apply some settings on Android.

Since dynamic analysis of Android applications (usually through hooks) is a core part of several mobile application security tests, the need of a tool that can help us do said tests is real. Even though there are other tools that promise to help you do that, I’ve run across some limitations when testing them:

* Lack of interaction with the user doing the tests;
* Only work in emulators;
* Plenty of time to update the tool after an Android update;
* Very poor output;
* Very costly setup.

With Inspeckage, we can get a good amount of information about the application’s behavior:

* Information Gathering
  * Requested Permissions
  * App Permissions
  * Shared Libraries
  * Exported and Non-exported Activities, Content Providers,Broadcast Receivers and Services
  * Check if the app is debuggable or not
  * Version, UID and GIDs
  * etc.

With the hooks, we can see what the application is doing in real time:

* Shared Preferences (log and file);
* Serialization;
* Crypto;
* Hashes;
* SQLite;
* HTTP (an HTTP proxy tool is still the best alternative);
* File System;
* Miscellaneous (Clipboard, URL.Parse());
* WebView;
* IPC.

### Important

#### Insecure Data Storage

We've totally interacted with our app now it's time to see the files created locally.

Many developers assume that storing data on client-side will restrict other users from having access to this data. Interestingly, most of the top mobile application security breaches have been caused by insecure or unnecessary client-side data storage. File systems on devices are no longer a sandboxed environment and rooting or jailbreaking usually circumvents any protections.

One needs to understand what different types of data are there and how are these stored insecurely.

Data - Usernames, Authentication tokens or passwords, Cookies, Location data, Stored application logs or Debug information, Cached application messages or transaction history, UDID or EMEI, Personal Information (DoB, Address, Social, etc), Device Name, Network Connection Name, private API calls for high user roles, Credit Card Data or Account Data, etc.

All apps (root or not) have a default data directory, which is /data/data/\<package\_name>. By default, the apps databases, settings, and all other data go here.

* **databases**/: here go the app's databases
* **lib**/: libraries and helpers for the app
* **files**/: other related files
* **shared\_prefs**/: preferences and settings
* **cache**/: well, caches

For interact with device or emulator

`adb shell`

Sqlite database file

Once you are able to access the SQLite database file on an emulator, rooted device or via adb shell / run as \[package name], there are a few options to inspect the schema and your SQLite database on device.

[**Inspect SQLite db via a GUI tool**](https://sqlitebrowser.org/dl/)

Pull the file from device first, then use a GUI software to look the schema and content. I use SQLite browser which allows you to see the database schema, table content, as well as executing some simple SQL scripts.

`adb pull /data/data/package-name/databases/sqlitedatabse`

**Inspect SQLite db via sqlite3 command line tool**

For me the easier option is to use sqlite3 command line tool to inspect the database from adb shell.

`cd data/data/package-name/databases/`

`sqlite3 db-name`

`.tables`

`.schema table-name`

#### Shared Preference Files

The SharedPreferences API is commonly used to permanently save small collections of key-value pairs. Data stored in a SharedPreferences object is written to a plain-text XML file. The SharedPreferences object can be declared world-readable (accessible to all apps) or private. Misuse of the SharedPreferences API can often lead to exposure of sensitive data. Consider the following example:

```
SharedPreferences sharedPref = getSharedPreferences("key", MODE_WORLD_READABLE);
SharedPreferences.Editor editor = sharedPref.edit();
editor.putString("username", "administrator");
editor.putString("password", "supersecret");
editor.commit();
```

Once the activity has been called, the file key.xml will be created with the provided data. This code violates several best practices.

```xml
<?xml version='1.0' encoding='utf-8' standalone='yes' ?>
<map>
  <string name="username">administrator</string>
  <string name="password">supersecret</string>
</map>xml
```

### Complications

#### Root Detection in Android Devices

Explanation for this is:

```
if(device && emulator = rooted):
    print "app going to the shit!"
else:
    print "app found"
```

So it is the best way to check in your application whether the device is rooted or not to avoid data theft but there’s no 100% way to check for root.

Check for Test-Keys: Test-Keys has to do with how the kernel is signed when it is compiled. By default, stock Android ROMs from Google are built with release-keys tags. Test-Keys means it is signed with a custom key generated by a third-party developer. Specifically, it will check in build properties(“android.os.Build.TAGS”) for test-keys.

```
private boolean detectTestKeys() {
    String buildTags = android.os.Build.TAGS;
    return buildTags != null && buildTags.contains("test-keys");
}
```

Check for “su” binary: Su binary check is to identify the superuser in the device. This binary is installed when you try to root your phone using apps like kinguser or via fastboot in Android. These files are necessary so that one can root their phone and become the superuser. The existence of this binary can be checked from the following paths.

```
private boolean checkForSuBinary() {
    return checkForBinary("su"); // function is available below
}
```

Check for “busybox” binary: If a device has been rooted, more often than not Busybox has been installed as well. Busybox is a binary that provides many common Linux commands. Running busybox is a good indication that a device has been rooted.

```
private boolean checkForBusyBoxBinary() {
   return checkForBinary("busybox");//function is available below
}
```

Check for SuExists: different file system check for the su binary.

```
private boolean checkSuExists() {
    Process process = null;
    try {
        process = Runtime.getRuntime().exec(new String[]
                {"/system /xbin/which", "su"});
        BufferedReader in = new BufferedReader(
                new InputStreamReader(process.getInputStream()));
        String line = in.readLine();
        process.destroy();
        return line != null;
    } catch (Exception e) {
        if (process != null) {
            process.destroy();
        }
        return false;
    }
}
```

The following paths, Su and busybox binaries are often looked for on rooted devices.

```
private String[] binaryPaths= {
        "/data/local/",
        "/data/local/bin/",
        "/data/local/xbin/",
        "/sbin/",
        "/su/bin/",
        "/system/bin/",
        "/system/bin/.ext/",
        "/system/bin/failsafe/",
        "/system/sd/xbin/",
        "/system/usr/we-need-root/",
        "/system/xbin/",
        "/system/app/Superuser.apk",
        "/cache",
        "/data",
        "/dev"
};
```

#### Emulator Detection

Explanation for this is:

```
if(isEmulator):
    print "app going to the shit!"
else:
    print "app found" 
```

First you need to check the the pre-decompiled source code and check for functions that contains strings like “generic | emulator | google\_sdk” and functions like “isEmulator | emulatorDetection…etc” … use your searching skills and read the code well

#### SSL Pinning

Is a technique that we use in the client side to avoid man-in-the-middle attack by validating the server certificates again even after SSL handshaking. The developers embed (or pin) a list of trustful certificates to the client application during development, and use them to compare against the server certificates during runtime. If there is a mismatch between the server and the local copy of certificates, the connection will simply be disrupted, and no further user data will be even sent to that server. This enforcement ensures that the user devices are communicating only to the dedicated trustful servers.

After you have taken in the illustration above, note that certificate pinning attempts to ensure that the client is not exchanging messages with any other server than the one they hold a public key for. Therefore, the client is not exposed to attacks where a rogue Certificate Authority (CA) validates the authenticity of a malicious host serving content with a sham certificate.

```
if(devices && emulators = CA_BURPSUITE):
    print "not intercept comunications ):"
else:
    print "App found"
```

### Complications Bypass

#### Hooking Applications

Techniques used to alter the behavior of applications.

#### Frida

Is a dynamic instrumentation framework, which enables function hooking and allows to provide a definition to it during runtime. Basically, it injects JavaScript code into a process. Suppose, there is a function called “foo” in a program with a specific body/implementation. Using “Frida”, one can change the body/implementation of the “foo” function during runtime. “Frida” supports a variety of platforms like Windows, macOS, GNU/Linux, iOS, Android, and QNX. More information on “Frida” can be found here.

Usage:

```bash
# Install
pip install frida-tools
# Now check version and download the server, in my case is 12.6.8
frida --version
# unzip file and push the server in the local system /data/local/tmp
adb push /path/serverfrida /data/local/tmp
# Permissions
adb shell chmod 777 /data/local/tmp/frida-server
# run frida server
adb shell /data/local/tmp/frida-servername&
# now execute in your command line
frida-ps -U
```

[**Bypass SSL pinning tutorial**](https://medium.com/@ved\_wayal/hail-frida-the-universal-ssl-pinning-bypass-for-android-e9e1d733d29)

## Report

## References

[Owasp Mobile Top 10](https://www.owasp.org/index.php/Mobile\_Top\_10\_2016-Top\_10)

[Mobile Security Testing Guide (MSTG)](https://www.owasp.org/index.php/OWASP\_Mobile\_Security\_Testing\_Guide)

[Mobile Security Twitter](https://twitter.com/mobilesecurity)
