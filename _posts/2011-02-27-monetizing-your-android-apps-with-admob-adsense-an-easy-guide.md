---
title: 'Monetizing your Android apps with AdMob (AdSense), an easy guide'
date: '2011-02-27T15:23:54+10:00'
permalink: /monetizing-your-android-apps-with-admob-adsense-an-easy-guide
author: 'James Elsey'
category:
    - Android
tag:
    - admob
    - adsense
    - android
    - monetization

---
Howdy folks, another nice and easy android guide here. If you’re like me you’re thinking about putting your first application up on the market. It might be a good idea to set-up some ads on application, hopefully making a few pennies as you go.

Its actually quite easy, if your familiar with AdSense for web sites it’ll be even more easier.

First thing you need to do, is head over to AdMob and create an account, should only take you a couple of minutes.

Once thats all setup, login and go to the “add sites” link and create an android site. You’ll be asked for some details on your app, if you don’t actually have it loaded into the marketplace yet, then just enter some dummy values since you can always modify these later.

On the next screen you should find a link to download the SDK, this is where we get started!

Download the SDK, and take the file “admob-sdk-android.jar” and put it into the libs/ directory of your application. You’ll also need to configure your IDE to put this onto the build path in order for it to be recognised.

Next we’ll need to configure our application to use the AdMob library, open up your AndroidManifest.xml file and add the following before the closing application tag.

```xml
<!-- The application's publisher ID assigned by AdMob -->
<meta-data android:value="YOUR_ID_HERE" android:name="ADMOB_PUBLISHER_ID" />
        
<!-- AdMobActivity definition -->
<activity android:name="com.admob.android.ads.AdMobActivity"
android:theme="@android:style/Theme.NoTitleBar.Fullscreen"
android:configChanges="orientation|keyboard|keyboardHidden" />
<!-- Track Market installs -->          
<receiver android:name="com.admob.android.ads.analytics.InstallReceiver"
android:exported="true">
<intent-filter>
   <action android:name="com.android.vending.INSTALL_REFERRER" />
</intent-filter>
</receiver>
```

You’ll need to replace `YOUR_ID_HERE` with your publisher ID. Thats easy to find, login to your AdMob account, click on [Sites](http://www.admob.com/my_sites/), then manage the settings on your application, you’ll see the code at the top of the page.

Also, make sure that your application has permissions to the internet, if it can’t access the web, how can it fetch ads?

```xml
<!-- AdMob SDK requires Internet permission -->
  <uses-permission android:name="android.permission.INTERNET" />
```

Next, if you don’t already have a file called res/values/attrs.xml, then create one with the following content. If you do already have that file, then work in the following

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
<declare-styleable name="com.admob.android.ads.AdView">
<attr name="backgroundColor" format="color" />
<attr name="primaryTextColor" format="color" />
<attr name="secondaryTextColor" format="color" />
<attr name="keywords" format="string" />
<attr name="refreshInterval" format="integer" />
</declare-styleable>
</resources>
```

Now thats all done, lets think about actually inserting some ads into your application. I’ve added the following as the last element in my LinearLayout in my res/layout/main.xml file, which means the adds will sit along the bottom like a footer

```xml
     <com.admob.android.ads.AdView
            android:id="@+id/ad"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            myapp:backgroundColor="#000000"
            myapp:primaryTextColor="#FFFFFF"
            myapp:secondaryTextColor="#CCCCCC"/>
```

It will probably show up in red text in your IDE, it does in mine, but it still compiles fine so its nothing to worry about.

Be sure to add the “myapp” namespace into the LinearLayout, it must point to your package, such as :

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
              xmlns:myapp="http://schemas.android.com/apk/res/com.jameselsey.LanguageSelection"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              android:orientation="vertical">
```

Thats it, build your APK and deploy it to a device and you’ll see your ads. Sometimes it will take ~10 seconds for the ad to display, I believe this is due to the app having to make a connection to AdMob to retrieve a suitable ad.

If you want to set some test ads, then add the following snippet into the onCreate method in your activity (the one that uses the view where we declared the AdView)

```java
 AdManager.setTestDevices(new String[]{
                AdManager.TEST_EMULATOR, // Android emulator
                "CF95DC53F383F9A836FD749F3EF439CD",
        });
```

The long horrible code is my device code, in order to find out what it is for your device, run the application with the following code in, and then look at LogCat, it should have the following line telling you what your device code is :

```
02-26 21:51:11.695: INFO/AdMobSDK(7591): To get test ads on this device use AdManager.setTestDevices( new String[] { "CF95DC53F383F9A836FD749F3EF439CD" } )
```

Using this test mode, it ensures that an ad is always placed in your app, and you don’t have to worry about internet connections and such, its essentially a placeholder.

Thats it, I hope I’ve helped you get setup and ready to earn a few pennies, please leave me a comment to let me know how you get on!

Further Reading
---------------

- [AdMob home](http://www.admob.com/)
- [AdMob README](http://www.admob.com/docs/AdMob_Android_SDK_Instructions.pdf)