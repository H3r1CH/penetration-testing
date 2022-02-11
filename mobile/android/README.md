# Android

## Introduction

### Native vs Hybrid Applications

Native: They are those developed applications only and exclusively for mobile operating systems, either Android or iOS. In Android you use the Java or kotlin programming language, while in iOS you make use of Swift or Objective-C. These programming languages are the official ones for the respective operating systems.

Hybrid: These applications use technologies such as HTML, CSS and JavaScript, all of these linked and processed through frameworks such as Apache Córdova "PhoneGap", Ionic, among others.

### Android SMALI Code

When you create an application code, the apk file contains a .dex file, which contains binary Dalvik bytecode. Smali is an assembly language that runs on Dalvik VM, which is Android's JVM.

{% tabs %}
{% tab title="Java" %}
```java
public static int simpleMethod(boolean param) {
    int x = 5;
    if (param) {
        return x;
    }
    return 0;
}
```
{% endtab %}

{% tab title="Smali" %}
```smali
.method public static simpleMethod(X)I
    .locals 1
    const/16 v0, 0x5 #@0
    if-eqz p0, :cond_0 #@2
    :goto_0
    return v0 #@4
    :cond_0
    const/4 v0, 0x0 #@5
    goto :goto_0 #@6
.end method
```
{% endtab %}
{% endtabs %}

### Smali Syntax Types

`.method private doSomething()V`

* V void
* Z boolean
* B byte
* S short
* C char
* F float
* I int
* J long
* D double
* \[ array

### Smali Registers by JesusFreke

#### Introduction

In dalvik's bytecode, registers are always 32 bits, and can hold any type of value. 2 registers are used to hold 64 bit types (Long and Double). Specifying the number of registers in a method

#### Specifying the number of registers in a method

There are two ways to specify how many registers are available in a method. the .registers directive specifies the total number of registers in the method, while the alternate .locals directive specifies the number of non-parameter registers in the method. The total number of registers would therefore include the registers needed to hold the method parameters.

#### How the method parameters are passed into a method

When a method is invoked, the parameters to the method are placed into the last n registers. If a method has 2 arguments, and 5 registers (v0-v4), the arguments would be placed into the last 2 registers - v3 and v4.

The first parameter to a non-static methods is always the object that the method is being invoked on.

For example, let's say you are writing a non-static method `LMyObject;->callMe(II)V`. This method has 2 integer parameters, but it also has an implicit LMyObject; parameter before both integer parameters, so there are a total of 3 arguments to the method.

Let's say you specify that there are 5 registers in the method (`v0`-`v4`), with either the `.registers 5` directive or the `.locals 2` directive (i.e. 2 _local_ registers + 3 parameter registers). When the method is invoked, the object that the method is being invoked on (i.e. the `this` reference) will be in `v2`, the first integer parameter will be in `v3`, and the second integer parameter will be in `v4`.

For static methods it's the same thing, except there isn't an implicit `this` argument.

#### Register names

There are two naming schemes for registers - the normal v naming scheme and the p naming scheme for parameter registers. The first register in the p naming scheme is the first parameter register in the method. So let's go back to the previous example of a method with 3 arguments and 5 total registers. The following table shows the normal v name for each register, followed by the p name for the parameter registers

#### Local Param

* v0 the first local register
* v1 the second local register
* v2 p0 the first parameter register
* v3 p1 the second parameter register
* v4 p2 the third parameter register

#### Motivation for introducing parameter registers

The p naming scheme was introduced as a practical matter, to solve a common annoyance when editing smali code.

Say you have an existing method with a number of parameters and you are adding some code to the method, and you discover that you need an extra register. You think "No big deal, I'll just increase the number of registers specified in the .registers directive!".

Unfortunately, it isn't quite that easy. Keep in mind that the method parameters are stored in the last registers in the method. If you increase the number of registers - you change which registers the method arguments get put into. So you would have to change the .registers directive and renumber every parameter register.

But if the p naming scheme was used to reference parameter registers throughout the method, you can easily change the number of registers in the method, without having to worry about renumbering any existing registers.

Note: by default baksmali will use the p naming scheme for parameter registers. If you want to disable this for some reason and force baksmali to always use the v naming scheme, you can use the -p/--no-parameter-registers option.

#### Long/Double values

As mentioned previously, long and double primitives (J and D respectively) are 64 bit values, and require 2 registers. This is important to keep in mind when you are referencing method arguments. For example, let's say you have a (non-static) method LMyObject;->MyMethod(IJZ)V. The parameters to the method are LMyObject;, int, long, bool. So this method would require 5 registers for all of its parameters.

#### Register Type

* p0 this
* p1 I
* p2, p3 J
* p4 Z

Also, when you are invoking the method later on, you do have to specify both registers for any double-wide arguments in the register list for the invoke-instruction.

`Application Structure. (APK)`

`![](<../../.gitbook/assets/image (1).png>)`

* **AndroidManifest.xml**: the manifest file in binary [XML](https://tryhackme.com/room/androidhacking101) format.
* **classes.dex:** application code compiled in the dex format.
* **resources.arsc:** file containing precompiled application resources, in binary [XML](https://tryhackme.com/room/androidhacking101).
* **res/:** folder containing resources not compiled into resources.arsc
* **assets/:** optional folder containing applications assets, which can be retrieved by AssetManager.
* **lib/:** optional folder containing compiled code - i.e. native code libraries.
* **META-INF/:** folder containing the MANIFEST.MF file, which stores meta data about the contents of the JAR. which sometimes will be store in a folder named original.The signature of the APK is also stored in this folder.
* Every APK file includes an AndroidManifest.xml file which declares the application’s package name, version components and other metadata. Full detail of Android manifest specs file can be view here. Below is just some common attributes that can identify in AndroidManifest.

| Attributes       | Notes                                                                                                                                                                                          |
| ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Manifest tag     | contains android installation mode, package name, build versions                                                                                                                               |
| Permissions      | custom permission and protection level                                                                                                                                                         |
| uses-permissions | requests a permission that must be granted in order for it to operate, full list of permission api can refer [here](https://developer.android.com/reference/android/Manifest.permission.html). |
| uses-feature     | Declares a single hardware or software feature that is used by the application.                                                                                                                |
| Application      | The declaration of the application. Will contains all the activity                                                                                                                             |
| Activity         | Declares an activity that implements part of the application visual user interface.                                                                                                            |
| intent-filter    | Specifies the types of intents that an activity, service, or broadcast receiver can respond to.                                                                                                |
| service          | Declare a service as one of the application components.                                                                                                                                        |
| receiver         | Broadcast receivers enable applications to receive intents that are broadcast by the system or by other applications, even when other components of the application are not running.           |
| provider         | Declares a content provider component. A content provider is a subclass of ContentProvider that supplies structured access to data managed by the application.                                 |
