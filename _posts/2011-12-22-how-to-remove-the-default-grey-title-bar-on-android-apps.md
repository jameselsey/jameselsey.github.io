---
title: 'How to remove the default grey title bar on android apps'
date: '2011-12-22T13:51:39+10:00'
permalink: /how-to-remove-the-default-grey-title-bar-on-android-apps
author: 'James Elsey'
category:
    - Android
tag:
    - android
    - android-ui
    - ui
    - user-interface
---
![title_bar_removal](/assets/post_images/2011/title_bar_removal.png)

Fed up of the default title bar on your android applications?

We can get rid of that easy, either use the following in your activities to do it :

```java
requestWindowFeature(Window.FEATURE_NO_TITLE);
```

Or, the way I prefer, disable it application wide by slipping the following into the AndroidManifest.xml file :

```xml
android:theme="@android:style/Theme.NoTitleBar"
```