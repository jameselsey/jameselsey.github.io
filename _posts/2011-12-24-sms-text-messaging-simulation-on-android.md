---
title: 'SMS text messaging simulation on android'
date: '2011-12-24T03:39:04+10:00'
permalink: /sms-text-messaging-simulation-on-android
author: 'James Elsey'
category:
    - Android
tag:
    - adb
    - android
    - sms
    - 'text messaging'

---
As part of an application I’m currently developing, I need to be able to interogate incoming SMS messages. Since I’ll be developing against the emulator initially I need a way of sending in SMS messages to the emulator. Fortunately, we can use adb to poke in SMS messages at will, as detailed below.

![sms-incoming](/assets/post_images/sms-incoming.jpg) Ultimately, you’ll need to telnet into your emulator, and you’ll need two things:- The port the emulator runs on. To find this out, open up a command window and type :

**adb devices**

You should see something like this :

```
C:\development\projects\AndroidSam\trunk_androidsam> adb devices  
List of devices attached  
emulator-5554 device
```

So my emulator is running on the default port of 5554.

- Secondly, if you’re a Windows 7 / Vista user, you probably won’t have telnet enabled by default, so switch that on by going into **Control Panel > Programs &gt; Programs and Features > Turn Windows features on or off.**

Now, lets actually send in an SMS. From the command window, type :

**telnet localhost 5554**

**sms send 07841123123 Hello this is a sample message**  
OK


Then have a look at your emulator, you should see the SMS come through.

Stick around, I’ll post up some more SMS related tips in the coming weeks!