---
title: 'How to install any application on the emulator'
date: '2012-05-24T19:04:22+10:00'
permalink: /how-to-install-any-application-on-the-emulator
author: 'James Elsey'
category:
    - Android
tag:
    - adb
    - android
    - apk
    - ddms
    - emulator
---
I’ve been developing an app recently that relies on being able to navigate and browse for images on the phone, which is all well and good when testing on a device as chances are you’ll have something like [Astro](https://play.google.com/store/apps/details?id=com.metago.astro&hl=en)installed.

This isn’t quite the case when developing on the emulator, since you’ll have just the stock version of android, which by default doesn’t come with a file browser (or at least not one you can easily interrogate).

The easiest thing to do, is just install a file browser (like Astro) onto the emulator….but wait…there is no market on the emulator so how do you do that?

Easy.

1. You’ll need the applications APK file, the easiest way to do this is to just install it onto your device from the marketplace
2. Connect your device to your development machine so that it is accessible via ddms.
3. In ddms, go *Device -&gt; File Explorer*
4. Look under */data/app* and search for the application you want to pull from the phone, then click the *“pull from device”* at the top left corner
5. Save the apk somewhere
6. Unplug your device, and start up the emulator
7. Type **adb devices** and you should see just the emulator attached
8. Now type **adb install <path apk="" to=""></path>**
9. Voila! Your APK is now installed on the emulator, rinse and repeat for other applications

![install-apk-manually](/assets/post_images/2012/install-apk-manually.jpg)