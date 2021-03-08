---
title: 'Android; how to actually put your applications APK onto a device'
date: '2010-11-14T20:34:08+10:00'
permalink: /android-how-to-actually-put-your-applications-apk-onto-a-device
author: 'James Elsey'
category:
    - Android
    - Java
tag: []
---
OK, so you’ve been developing your “hello world” application in Eclipse for a little while, and now want to actually test it out on a device.

The first thing you need to do is to create your APK file, this is the bundled application file and gets installed similar to how an EXE file would on Windows. Check your working directory area, under the bin directory you should find your .apk file that eclipse (or a more superior IDE) will build for you each time you run it.

Connect your phone to your PC, and copy the APK file over to the device. Next go onto the market place and search for an application called “app installer”. Once you’ve downloaded this, use is to navigate to your APK file and install it. Simple as that really!

Some things that you may be interested in, check your AndroidManifest.xml file for the following section in the application tag

```xml
android:icon="@drawable/myLogo"
android:label="@string/app_name"
android:name="com.jameselsey.mecercana.domain.GlobalState"
android:theme="@android:style/Theme.NoTitleBar"

```

You can use the android:icon to set the actual icon your app will display in the apps menu. the android:label will correspond to the applications name. So if you want a fancy icon, set it up here