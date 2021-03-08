---
title: 'Android; Implementing global state; share data between Activities and across your application'
date: '2010-09-21T22:50:51+10:00'
permalink: /android-implementing-global-state-share-data-between-activities-and-across-your-application
author: 'James Elsey'
category:
    - Android
    - Java
tag: []
---
If your developing an Android application, chances are you will have multiple activities that perform various tasks. One of my first questions when I was investigating Android, was how can I pass data between my Activities? Moreover, how can I retain some form of state, globally throughout my application, so I can dynamically pass data without having to persist to a datastore between Activities.

The solution; Use the applications [Context](http://developer.android.com/reference/android/content/Context.html)

Firstly, you should create a new class that will behave as our global object, for example :

```java
package com.jameselsey.domain;

import java.util.ArrayList;
import java.util.List;
import com.google.android.maps.GeoPoint;
import android.app.Application;

/**
* This is a global POJO that we attach data to which we
* want to use across the application
* @author James Elsey
*
*/
public class GlobalState extends Application
{
private String testMe;

public String getTestMe() {
return testMe;
}
public void setTestMe(String testMe) {
this.testMe = testMe;
}
}
```

The above class declares one String, with accessor methods. Soon we will see how this object will be available throughout the application. You can add anything you want onto this object, in my application I have a List of other domain classes that I wanted to use elsewhere in my application.

Right, now that you have your global class defined, you need to specify this in your AndroidManifest file, otherwise the application won’t know where to find this class, and you will get a ClassNotFoundException

Locate your application tag, and add this into it :

```
android:name="com.jameselsey.domain.GlobalState"
```

It must reference your fully qualified global state class

Now you have it set, your ready to start putting data in, and pulling data out, use the following

```java
// Set values
GlobalState gs = (GlobalState) getApplication();
gs.setTestMe("Some String");</code>

// Get values
GlobalState gs = (GlobalState) getApplication();
String s = gs.getTestMe();
```

And thats it! Off you go!