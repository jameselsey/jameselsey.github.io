---
title: 'How to place your ads at the bottom of your screen in Android'
date: '2011-02-27T21:51:37+10:00'
permalink: /how-to-place-your-ads-at-the-bottom-of-your-screen-in-android
author: 'James Elsey'
category:
    - Android
tag:
    - adsense
    - adwhirl
    - android
    - monetization
---
So you’ve setup some ads using AdMob or AdWhirl for your android application, and you’re having a hard time getting the ad to appear at the bottom of your screen? Well fear not, for we have a solution.

What you need to do, is to have a RelativeLayout, and within that, have one LinearLayout for your own views, and another LinearLayout which just contains the ads, and anchor that to the bottom of the screen.

Have a look at my example here :

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
                xmlns:androidsam="http://schemas.android.com/apk/res/com.jameselsey"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:orientation="vertical">
    <LinearLayout android:layout_width="fill_parent"
                  android:id="@+id/home_layout"
                  android:orientation="vertical"
                  android:layout_height="wrap_content">
        <!-- Put all your application views here, such as buttons, textviews, edittexts and so on -->

    </LinearLayout>
    <LinearLayout android:layout_width="fill_parent"
                  android:id="@+id/ad_layout"
                  android:layout_height="wrap_content"
                  android:gravity="bottom"
                  android:layout_alignParentBottom="true"
                  android:layout_alignBottom="@+id/home_layout">
        <com.admob.android.ads.AdView
                android:id="@+id/ad"
                android:layout_width="fill_parent"
                android:layout_height="wrap_content"
                android:gravity="bottom"
                androidsam:backgroundColor="#000000"
                androidsam:primaryTextColor="#FFFFFF"
                androidsam:secondaryTextColor="#CCCCCC"/>
    </LinearLayout>
</RelativeLayout>
```

Hope this helps!