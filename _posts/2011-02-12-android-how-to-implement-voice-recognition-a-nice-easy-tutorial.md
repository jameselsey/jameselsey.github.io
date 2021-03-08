---
title: 'Android; How to implement voice recognition, a nice easy tutorial&#8230;'
date: '2011-02-12T14:43:19+10:00'
permalink: /android-how-to-implement-voice-recognition-a-nice-easy-tutorial
author: 'James Elsey'
category:
    - Android
    - Java
tag:
    - android
    - input
    - recognition
    - speech
    - voice
---
I’ve been searching for a simple tutorial on using voice recognition in Android but haven’t had much luck. The Google official documentation provide an example of the activity, but don’t speculate anything more than that, so you’re kind of on your own a little.

Luckily I’ve gone through some of that pain, and should make this easy for you, post up a comment below if you think I can improve this.

I’d suggest that you create a blank project for this, get the basics, then think about merging VR into your existing applications. I’d also suggest that you copy the code below exactly as it appears, once you have that working you can begin to tweak it.

With your blank project setup, you should have an AndroidManifest file like the following

```xml
<pre class="brush: xml; title: ; notranslate" title="">
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
      package="com.jameselsey"
      android:versionCode="1"
      android:versionName="1.0">
    <application android:label="VoiceRecognitionDemo" android:icon="@drawable/icon"
            android:debuggable="true">
        <activity android:name=".VoiceRecognitionDemo"
                  android:label="@string/app_name">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
</manifest>
```

And have the following in res/layout/voice\_recog.xml :

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical">
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:paddingBottom="4dip"
        android:text="Click the button and start speaking" />
    <Button android:id="@+id/speakButton"
        android:layout_width="fill_parent"
        android:onClick="speakButtonClicked"
        android:layout_height="wrap_content"
        android:text="Click Me!" />
    <ListView android:id="@+id/list"
        android:layout_width="fill_parent"
        android:layout_height="0dip"
        android:layout_weight="1" />
 </LinearLayout>
```

And finally, this in your res/layout/main.xml :

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    >
<TextView
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:text="VoiceRecognition Demo!"
    />
</LinearLayout>
```

So thats your layout and configuration sorted. It will provide us with a button to start the voice recognition, and a list to present any words which the voice recognition service thought it heard. Lets now step through the actual activity and see how this works.

You should copy this into your activity :

```java
package com.jameselsey;
import android.app.Activity;
import android.os.Bundle;
import android.content.Intent;
import android.content.pm.PackageManager;
import android.content.pm.ResolveInfo;
import android.speech.RecognizerIntent;
import android.view.View;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.ListView;
import java.util.ArrayList;
import java.util.List;
/**
 * A very simple application to handle Voice Recognition intents
 * and display the results
 */
public class VoiceRecognitionDemo extends Activity
{
    private static final int REQUEST_CODE = 1234;
    private ListView wordsList;
    /**
     * Called with the activity is first created.
     */
    @Override
    public void onCreate(Bundle savedInstanceState)
    {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.voice_recog);
        Button speakButton = (Button) findViewById(R.id.speakButton);
        wordsList = (ListView) findViewById(R.id.list);
        // Disable button if no recognition service is present
        PackageManager pm = getPackageManager();
        List<ResolveInfo> activities = pm.queryIntentActivities(
                new Intent(RecognizerIntent.ACTION_RECOGNIZE_SPEECH), 0);
        if (activities.size() == 0)
        {
            speakButton.setEnabled(false);
            speakButton.setText("Recognizer not present");
        }
    }
    /**
     * Handle the action of the button being clicked
     */
    public void speakButtonClicked(View v)
    {
        startVoiceRecognitionActivity();
    }
    /**
     * Fire an intent to start the voice recognition activity.
     */
    private void startVoiceRecognitionActivity()
    {
        Intent intent = new Intent(RecognizerIntent.ACTION_RECOGNIZE_SPEECH);
        intent.putExtra(RecognizerIntent.EXTRA_LANGUAGE_MODEL,
                RecognizerIntent.LANGUAGE_MODEL_FREE_FORM);
        intent.putExtra(RecognizerIntent.EXTRA_PROMPT, "Voice recognition Demo...");
        startActivityForResult(intent, REQUEST_CODE);
    }
    /**
     * Handle the results from the voice recognition activity.
     */
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data)
    {
        if (requestCode == REQUEST_CODE &amp;&amp; resultCode == RESULT_OK)
        {
            // Populate the wordsList with the String values the recognition engine thought it heard
            ArrayList<String> matches = data.getStringArrayListExtra(
                    RecognizerIntent.EXTRA_RESULTS);
            wordsList.setAdapter(new ArrayAdapter<String>(this, android.R.layout.simple_list_item_1,
                    matches));
        }
        super.onActivityResult(requestCode, resultCode, data);
    }
}
```

**Breakdown of what the activity does :**

Declares a request code, this is basically a checksum that we use to confirm the response when we call out to the voice recognition engine, this value could be anything you want. We also declare a ListView which will hold any words the recognition engine thought it heard.

The onCreate method does the usual initialisation when the activity is first created. This method also queries the packageManager to check if there are any packages installed that can handle intents for ACTION\_RECOGNIZE\_SPEECH. The reason we do this is to check we have a package installed that can do the translation, and if not we will disable the button.

The speakButtonClicked is bound to the button, so this method is invoked when the button is clicked. I wrote [another tutorial on button binding](/binding-your-buttons-to-your-activity-the-easy-way) so have a look at that if you don’t understand this method.

The startVoiceRecognitionActivity invokes an activity that can handle the voice recognition, setting the language mode to free form (as opposed to web form)

The onActivityResult is the callback from the above invocation, it first checks to see that the request code matches the one that was passed in, and ensures that the result is OK and not an error.

Next, the results are pulled out of the intent and set into the ListView to be displayed on the screen.

**Notes on debugging :**

You won’t have a great deal of luck running this on the emulator, there may be ways of using a PC microphone to direct audio input into the emulator, but that doesn’t sound like a trivial task. Your best bet is to generate the APK and transfer it over to your device (I’m running an Orange San Francisco / ZTE Blade).

If you experience any crashing errors (I did), then connect your device via USB, enable debugging, and then run the following command from a console window

```
<android-home>/platform-tools/adb -d logcat
```

What this does, is invoke the android device bridge, the -d switch specifies to run this against the physical device, and the logcat tells the ADB to print out any device logging to the console.

This means anything you do on the device will be logged into your console window, it helped me find a few null pointer issues.

That’s pretty much it, its quite a simple activity. Have a play with it and if you have any comments please let me know. You may have mixed results with what the recognition thinks you have said, I’ve had some odd surprises, let me know!

Happy coding!

Further Reading : 
------------------

1. [ADB Commands](http://developer.android.com/guide/developing/tools/adb.html)
2. [Official documentation](http://developer.android.com/resources/articles/speech-input.html)
