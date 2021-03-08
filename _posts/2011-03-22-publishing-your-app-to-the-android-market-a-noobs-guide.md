---
title: 'Publishing your app to the Android Market, a noobs guide'
date: '2011-03-22T17:23:55+10:00'
permalink: /publishing-your-app-to-the-android-market-a-noobs-guide
author: 'James Elsey'
category:
    - Android
tag:
    - android
    - market
    - publish
---
Hola a todos! Its been a little while since I last posted, mostly because I’ve been busy getting my first app in a state thats good enough to be published, in which I have, and heres a little write up on how easy it is to actually publish.

If you head over to the [official documentation](http://developer.android.com/guide/publishing/preparing.html), you’ll get inundated with instructions, I’ve already been through the pain of figuring it out, so I’ll regurgitate that in a real easy friendly way. As a quick overview, you’ll need to do the following :

1. Remove any logging, remove the “debuggable” option in your manifest, set your version numbers
2. Build your final APK
3. Sign the APK with a key
4. Zipalign the APK
5. Register on the market ($25 fee), fill out the form and publish the app

Sounds a little scary, however, if you follow my super secret-squirrel tips, you can do 2,3 & 4 in one simple ant command, lets have a look at each step now. (But before that, lets have a little Picard humour, its necessary)

![Picard android phone](/assets/post_images/2011/2010-10-05-08-54-22855614154.jpg)

Remove any logging, remove the “debuggable” option in your manifest, set your version numbers
---------------------------------------------------------------------------------------------

Fairly simple step this is, logging is irrelevant for a published app, so it should be removed. Its up to you if you want to actually delete out all the lines which contain Log.logCat();, or just comment them out. I opted for commenting them out, as I have relatively few of them and to be honest, it wasn’t really a big deal since its only me that works on it, so I know whats what.

Next, if you’ve been debugging on a device, you’ll most likely have the android:debuggable option set in your AndroidManifest.xml file, it needs to be removed.

```xml
<application android:label="LanguageSelection" android:icon="@drawable/creep_003" android:debuggable="true">
...
</application>
```

After that, you just need to setup the versioning parameters for your application, in your AndroidManifest.xml file you’ll have the following at the top :

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          package="com.jameselsey.apps.androidsam"
          android:versionCode="1"
          android:versionName="1.0">
...
</manifest>
```

Its really up to you what versioning strategy you use, I’ve opted to release my first version as 1.0, and any subsequent patches/bugfixes as minor releases, so 1.1, 1.2 and so on. A full release (i.e., new content) would be 2.0, 3.0 etc. Whatever you choose, make sure you’re consistent.

Thats it with the tweaking of your application, now comes onto getting a release ready.

Build your final APK
--------------------

Before we come onto farting around with APKs, lets have a little chat about the signing process. Every app that gets installed to an emulator or a device MUST be signed, even if you create a new hello world app and run it on the emulator, its been signed automatically behind your back, without you knowing. When you’re developing, your apps automatically get signed using a debug keystore, which means they will work on the emulator/device OK, however when you come to release your app to the market, you need to provide a proper, genuine 24 carat gold self signed certificate, in other words, a debug key for a live app just won’t cut it.

Creating a key is simple, just create a keystore if you don’t already have one. I decided to put mine in the root of my project structure, use the following command :

```
C:\development\projects\AndroidSam_trunk>keytool -genkey -v -keystore my-release-key.keystore
Enter keystore password:
Re-enter new password:
What is your first and last name?
  [Unknown]:  James Elsey
What is the name of your organizational unit?
  [Unknown]:  Individual Developer
What is the name of your organization?
  [Unknown]:  None
What is the name of your City or Locality?
  [Unknown]:  Ipswich
What is the name of your State or Province?
  [Unknown]:  Suffolk
What is the two-letter country code for this unit?
  [Unknown]:  UK
Is CN=James Elsey, OU=Individual Developer, O=None, L=Suffolk, ST=Suffolk, C=UK correct?
  [no]:  yes

Generating 1,024 bit DSA key pair and self-signed certificate (SHA1withDSA) with a validity of 90 days
        for: CN=James Elsey, OU=Individual Developer, O=None, L=Suffolk, ST=Suffolk, C=UK
Enter key password for <mykey>
        (RETURN if same as keystore password):
[Storing my-release-key.keystore]

```

As you can see, it’ll prompt you for some extra info, but otherwise its straightforward.

Now comes the meaty part, tieing all this into our build process.

Sign the APK with a key (part of the build process)
---------------------------------------------------

In Eclipse (or preferably, IntelliJ), have a look for the file build.properties. This file will probably all be commented out, but add these two properties to the bottom. Make sure the key store points to where you created the keystore, in my case it was in the root directory.

```
key.store=./my-release-key.keystore
key.alias=mykeystore
```

Thats all the configuration you need to do for this part.

Zipalign the APK (after running the build process)
--------------------------------------------------

OK, so so far we have created a keystore, and told our build properties where it can find it, lets actually create a build. Since you’ve already got [Ant](http://ant.apache.org/) installed (at least I’m assuming you do), its just a case of running the ant release target, such as follows :

```
C:\development\projects\AndroidSam_trunk>ant release
Buildfile: C:\development\projects\AndroidSam_trunk\build.xml
    [setup] Android SDK Tools Revision 9
    [setup] Project Target: Android 2.1-update1
    [setup] API level: 7
    [setup]
    [setup] ------------------
    [setup] Resolving library dependencies:
    [setup] No library dependencies.
    [setup]
    [setup] ------------------
    [setup]
    [setup]
    [setup] Importing rules file: tools\ant\main_rules.xml

-set-release-mode:
     [echo] *************************************************
     [echo] ****  Android Manifest has debuggable=true   ****
     [echo] **** Doing DEBUG packaging with RELEASE keys ****
     [echo] *************************************************

-release-obfuscation-check:

-dirs:
     [echo] Creating output directories if needed...

-pre-build:

-resource-src:
     [echo] Generating R.java / Manifest.java from the resources...

-aidl:
     [echo] Compiling aidl files into Java classes...

-pre-compile:

compile:
    [javac] C:\development\tools\androidSDK\android-sdk-windows\tools\ant\main_rules.xml:361: warning: 'includeantruntime' was not
set, defaulting to build.sysclasspath=last; set to false for repeatable builds
    [javac] Compiling 1 source file to C:\development\projects\AndroidSam_trunk\bin\classes

-post-compile:

-obfuscate:

-dex:
     [echo] Converting compiled files and external libraries into C:\development\projects\AndroidSam_trunk\bin\classes.dex...

-package-resources:
     [echo] Packaging resources
     [aapt] Creating full resource package...
     [null] Warning: AndroidManifest.xml already defines debuggable (in http://schemas.android.com/apk/res/android); using existing
 value in manifest.

-package-release:
[apkbuilder] Creating AndroidSam-unsigned.apk for release...

-release-prompt-for-password:
    [input] Please enter keystore password (store:./my-release-key.keystore):
<your password here>
    [input] Please enter password for alias 'mykeystore':
<your password here>

-release-nosign:

release:
     [echo] Signing final apk...
  [signjar] Signing JAR: C:\development\projects\AndroidSam_trunk\bin\AndroidSam-unsigned.apk to C:\development\projects\AndroidSam
_trunk\bin\AndroidSam-unaligned.apk as mykeystore
     [echo] Running zip align on final apk...
     [echo] Release Package: C:\development\projects\AndroidSam_trunk\bin\AndroidSam-release.apk

BUILD SUCCESSFUL
Total time: 16 seconds
```

That’s all you need to do, as you can see, my final (build, signed and aligned) apk is located at C:\\development\\projects\\AndroidSam\_trunk\\bin\\AndroidSam-release.apk, so all I need to do is upload that file into the market place! Easy.

Register on the market ($25 fee), fill out the form and publish the app
-----------------------------------------------------------------------

OK so we’re at the final hurdle now, one last leap and our app is out there on the interwebz, you’ll need to head over to the android marketplace and register yourself as a developer. There is a one time fee of $25 which you can pay with via your Google checkout account. This all ties into your GMail account so no worries about new accounts and such. There is an extra process you need to go through if you intend to sell your apps, but if you are listing for free, the developer account is sufficient.

Click “Upload Application”, fill out the form, and that is it! You will need to provide a description of your app, some icons, and select which category it should be listed under.

Done, yay! Picard demands that you join him in celebration!

![picard_win](/assets/post_images/2011/picard_win.jpg)

My experiences, tips and advice
-------------------------------

Theres a few things I’ve found from publishing my first app, hopefully they help others:

1. The documentation on Google is overwhelming, they make it seem much harder than it actually is.
2. Installing Ant and using the ant release target saves any manual signing/aligning and has made my life MUCH easier.
3. The $25 fee is a bit cheeky, but as a developer you need to constantly up your game, think of it as a future investment into your career.

Further Reading
---------------

1. [Google Documentation – Preparing to publish](http://developer.android.com/guide/publishing/preparing.html)
2. [Android Marketplace Home](http://market.android.com/publish/Home)
3. [Apache Ant](http://ant.apache.org/)
4. [Installing Apache Ant](http://ant.apache.org/manual/index.html)
5. [AndroidSam – My app on the market](https://market.android.com/details?id=com.jameselsey.apps.androidsam&feature=search_result)