---
title: 'Android; how to display a map the easy way..'
date: '2011-02-04T20:01:07+10:00'
permalink: /android-how-to-display-a-map-the-easy-way
author: 'James Elsey'
category:
    - Android
    - Java
tag:
    - activity
    - android
    - api
    - google
    - map
    - MapActivity

---
I’m seeing countless questions, literally on a daily basis on [StackOverflow ](http://www.stackoverflow.com)regarding using maps on Android. To be honest I’ve never come across these problems but it seems many people have trouble using the maps in their application, so I’ll provide a clear and easy set of instructions on how to do this.

**Common issues**

1. [API key](http://code.google.com/android/maps-api-signup.html) is incorrect
2. You are using the standard Android emulator and not the Google APIs.
3. You have extended Activity instead of MapActivity

The Tutorial…
-------------

The first thing you need, is an application to put your map in. For this tutorial I’m suggesting that you just create a blank android project, to get familiar with how maps work, then you can shift it into your existing application. Using IntelliJ (or Eclipse, if you must), create a new android project. I’ve called mine “MapMe”.

This will give you just one activity, such as the following :

```java
package com.jameselsey.mapme;

import android.app.Activity;
import android.os.Bundle;

public class MapMe extends Activity
{
    /** Called when the activity is first created. */
    @Override
    public void onCreate(Bundle savedInstanceState)
    {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
    }
}
```

I’d also suggest that you run this auto generated project to make sure your emulator starts up OK and you generally have a stable platform to build upon.

Next, we need to alter the emulator. The standard emulator that you get is the “android emulator”, which doesn’t allow you to use maps (at least not easily). This means that you need to create a slightly different emulator which does allow you to use maps, its quite easy.

Open up the android AVD manager (where you can create emulators / Android Virtual Devices). Click on the “available packages” option and download something called “Google APIs”, that should take a few minutes. When that is done, create a new AVD, be sure to check the “target” and make sure it is set to Google APIs. Once you’ve setup this emulator, make sure that your project is actually set to use it, by checking your run configurations and the target emulator.

![google_apis_emulator](/assets/post_images/2011/google_apis_emulator.png)

Once thats done, I’d suggest you re-run the base application we’ve just created to make sure its all still OK.

OK so before we can finally jump into some code, you’ll need to go off and get an API key. This key allows you to use the Google maps API, I won’t explain how to do that here since theres already a[ rather good writeup](http://code.google.com/android/add-ons/google-apis/mapkey.html) from Google themselves, so go off and do that now.

Right lets hop into some code again, first thing you need to do is to add INTERNET permissions and uses-library into your AndroidManifest.xml file, mine looks as follows :

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
      package="com.jameselsey.mapme"
      android:versionCode="1"
      android:versionName="1.0">
    <application android:label="MapMe" android:icon="@drawable/icon">
        <activity android:name="MapMe"
                  android:label="@string/app_name">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <uses-library android:name="com.google.android.maps" />
    </application>
    <uses-permission android:name="android.permission.INTERNET" />
</manifest> 

```

Next, go into main.xml under res/layout and add the following :

```xml
<com.google.android.maps.MapView
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/mapview"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:clickable="true"
    android:apiKey="YOUR_API_KEY_HERE"
/>
```

Now we can start doctoring our activity.

Firstly, add the following import

```java
import com.google.android.maps.MapActivity;
```

And make sure that you implement the method you inherit from MapActivity :

```java
@Override
    protected boolean isRouteDisplayed()
    {
        return false;
    }
```

That is pretty much it, all you need to do now is to run the application, you should have the following end result :

![maps_complete](/assets/post_images/2011/maps_complete.png)

Complete Source
---------------

**AndroidManifest.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
      package="com.jameselsey.mapme"
      android:versionCode="1"
      android:versionName="1.0">
    <application android:label="MapMe" android:icon="@drawable/icon">
        <activity android:name="MapMe"
                  android:label="@string/app_name">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <uses-library android:name="com.google.android.maps" />
    </application>
    <uses-permission android:name="android.permission.INTERNET" />
</manifest> 
```

**res/layout/main.xml**

```xml
<com.google.android.maps.MapView
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/mapview"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:clickable="true"
    android:apiKey="YOUR_API_KEY_HERE"
/>
```

**MapMe Activity**

```java
package com.jameselsey.mapme;

import android.os.Bundle;
import com.google.android.maps.MapActivity;

public class MapMe extends MapActivity
{
    /** Called when the activity is first created. */
    @Override
    public void onCreate(Bundle savedInstanceState)
    {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
    }

    @Override
    protected boolean isRouteDisplayed()
    {
        return false;
    }
}
```

Common Issues
-------------

### Problems with RuntimeException : stub

```
java.lang.RuntimeException: stub
   at com.google.android.maps.MapView.<init> (Unknown Source)
```

You need to make sure that

```xml
<uses-library android:name="com.google.android.maps" />
```

is a child of the application tag.

### Problems with importing of the com.google.\* package

You may come across the following error :

```
java.lang.IllegalAccessError: Class ref in pre-verified class resolved to unexpected implementation
```

If there are problems with that import (com.google.\*), chances are you don’t have the maps.jar on your class path. Go back into your AVD manager, check available packages, under 3rd party libraries you should find some libraries from Google Inc., download those. If that still doesn’t solve your issue, then double check that your android “facet” is correctly using the Google APIs target.

![facet_intelliJ](/assets/post_images/2011/facet_intelliJ.png)

Also, make sure that you don’t have 2 copies of the maps.jar on your class path, if you already have the target specified to Google APIs, then you don’t need to implicitly specify maps.jar on the classpath.

### Maps are displaying, but all I see is grey squares…

This is most likely because you have the wrong API key, please go and re-generate that again to make sure its OK.

### Further Reading

1. [Official Google Documentation](http://developer.android.com/resources/tutorials/views/hello-mapview.html)
2. [MobiForge Tutorial](http://mobiforge.com/developing/story/using-google-maps-android)