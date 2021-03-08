---
title: 'Converting UK Postcodes to co ordinates to use in Android'
date: '2010-09-21T22:50:04+10:00'
permalink: /converting-uk-postcodes-to-co-ordinates-to-use-in-android
author: 'James Elsey'
category:
    - Android
    - Java
tag: []
---
If your developing applications on Android, and are looking at using the [Maps API](http://developer.android.com/guide/topics/location/index.html), chances are you are going to want to plot items on the map, and in order to do that you need to know the longtitude and latitude of the location you wish to mark.

I’ll assume that you already have a solid base to work from, as in a working Android application with a [MapView ](http://code.google.com/android/add-ons/google-apis/reference/com/google/android/maps/MapView.html)enabled.

To locate an item on the map you need to use a [GeoPoint](http://code.google.com/android/add-ons/google-apis/reference/com/google/android/maps/GeoPoint.html), example below

```java
private float longtitude;
private float latitude;
GeoPoint p = new GeoPoint((int)(latitude * 1E6), (int)(longtitudel * 1E6));
```

Notice the usage of float values, the easiest way of getting from a postcode to a co ordinate is to obtain the longtitude and latitude values from a conversion website, and then to add it to the above forumla. The reason being is because it is incredibly difficult to find the int values of a co ordinates, so it is easier to get a float value and convert

In brief, do the following

1. Go to [MyGeoPosition.com](http://www.mygeoposition.com/ "MyGeoPosition.com") and enter your postcode
2. From the URLs it generates, take the longtitude and latitude values, then enter them in the above float variables
3. Generate your GeoPoint using the above formula
4. Start plotting!