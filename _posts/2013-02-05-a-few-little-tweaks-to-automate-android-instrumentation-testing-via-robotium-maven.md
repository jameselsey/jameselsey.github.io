---
title: 'A few little tweaks to automate Android instrumentation testing via Robotium &#038; Maven'
date: '2013-02-05T21:03:37+10:00'
permalink: /a-few-little-tweaks-to-automate-android-instrumentation-testing-via-robotium-maven
author: 'James Elsey'
category:
    - Java
tag:
    - android
    - 'automated testing'
    - robotium
---
Having recently revived an android project I haven’t opened in close to 6 months, I was left scratching my head as to why I couldn’t run any of my integration tests.

Thinking back, I remembered having problems getting robotium to instrument the clicking of a button, as simple as it sounds, theres a few little gotchas involved. Firstly, I needed to modify the test code to click on text, rather than a button, as it has been suggested by various users on [StackOverflow](http://stackoverflow.com/questions/5930239/clickonbutton-not-working-in-robotium) that there does tend to be odd side effects when clicking buttons:

```java
public void testClickGetSurveyTakesToSurveyRunner() {
 solo.clickOnText("Get Survey");
 solo.assertCurrentActivity("Current activity was not correct", SurveyRunner.class);
 }
```

Next, I needed to add this into the test projects AndroidManifest

```xml
<uses-sdk android:targetSdkVersion="10" />
```

And then to add this into the main projects AndroidManifest:

```xml
<supports-screens android:anyDensity="true"/> 
```

Both of these tweaks are mentioned on the [Robotium FAQ](http://code.google.com/p/robotium/wiki/QuestionsAndAnswers), so it might be worth glancing there to see if any enhancements get made in the future

After making those changes, I stumbled into another error that reared its ugly head in the logcat:

```
ERROR/AndroidRuntime(1185): FATAL EXCEPTION: main
 java.lang.IllegalAccessError: Class ref in pre-verified class resolved to unexpected implementation
 at com.roosearch.android.activity.SurveyRunner.onResume(SurveyRunner.java:38)
 at android.app.Instrumentation.callActivityOnResume(Instrumentation.java:1150)
 at android.app.Activity.performResume(Activity.java:3832)
 at android.app.ActivityThread.performResumeActivity(ActivityThread.java:2110)
 at android.app.ActivityThread.handleResumeActivity(ActivityThread.java:2135)
 at android.app.ActivityThread.handleLaunchActivity(ActivityThread.java:1668)
 at android.app.ActivityThread.access$1500(ActivityThread.java:117)
 at android.app.ActivityThread$H.handleMessage(ActivityThread.java:931)
 at android.os.Handler.dispatchMessage(Handler.java:99)
 at android.os.Looper.loop(Looper.java:130)
 at android.app.ActivityThread.main(ActivityThread.java:3683)
 at java.lang.reflect.Method.invokeNative(Native Method)
 at java.lang.reflect.Method.invoke(Method.java:507)
 at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:839)
 at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:597)
 at dalvik.system.NativeStart.main(Native Method)
```

There seems to be a rather peculiar requirement, that any dependencies your using on your main application, also need to be in the test projects pom.xml, but marked with a scope of provided. After making that change as advised [here](http://code.google.com/p/maven-android-plugin/issues/detail?id=142) and rebuilding, both the app and instrumentation app build and test successfully.

I was also pleasantly surprised that I could run the instrumentation tests via IntelliJ IDEA 12 Ultimate, last time I tried that (on an older version albeit), it wasn’t so simple…