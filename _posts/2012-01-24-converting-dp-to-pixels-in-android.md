---
title: 'Converting dp to pixels in android'
date: '2012-01-24T14:23:18+10:00'
permalink: /converting-dp-to-pixels-in-android
author: 'James Elsey'
category:
    - Android
tag:
    - android
    - pixels
    - ui
---
Sometimes in android you have to deal with pixels, which can often be awkward, such as view.setPadding(int, int, int, int). Obviously this is not ideal as pixel ratings vary from device to device, nonetheless there is a work around for this

Simply come up with the value you want in dip (density independent pixels), such as 10, and then convert into pixels using the display metrics of the device you’re working on. This will ensure that when the conversion runs, it’ll always scale to the device the application is running on.

```java
float sizeInDip = 10f;
int padding = (int) TypedValue.applyDimension(TypedValue.COMPLEX_UNIT_DIP, sizeInDip, getResources().getDisplayMetrics());
```

Enjoy!