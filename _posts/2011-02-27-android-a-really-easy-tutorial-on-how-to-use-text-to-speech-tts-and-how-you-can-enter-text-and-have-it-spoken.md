---
title: 'Android; A really easy tutorial on how to use Text To Speech (TTS) and how you can enter text and have it spoken'
date: '2011-02-27T17:32:14+10:00'
permalink: /android-a-really-easy-tutorial-on-how-to-use-text-to-speech-tts-and-how-you-can-enter-text-and-have-it-spoken
author: 'James Elsey'
category:
    - Android
    - Java
tag:
    - android
    - speech
    - text
    - to
    - tts

---
Hello readers, time for another little android post of mine. I’ve been working hard recently on some voice recognition and TTS work, so figured I’d post up some tutorials for people to see how easy it is. This is also my first post where I host content on Github so please bear with me!

TTS is actually incredibly easy, take a look at this first example activity :

```java

/**
 * Author : James Elsey
 * Date : 26/Feb/2011
 * Title : TextToSpeechDemo
 * URL : Http://www.JamesElsey.co.uk
 */
package com.jameselsey.demo.texttospeechdemo;

import android.app.Activity;
import android.content.Intent;
import android.os.Bundle;
import android.speech.tts.TextToSpeech;

/**
 * This class demonstrates checking for a TTS engine, and if one is
 * available it will spit out some speak.
 */
public class Main extends Activity implements TextToSpeech.OnInitListener
{
    private TextToSpeech mTts;
    // This code can be any value you want, its just a checksum.
    private static final int MY_DATA_CHECK_CODE = 1234;

    /**
     * Called when the activity is first created.
     */
    @Override
    public void onCreate(Bundle savedInstanceState)
    {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);

        // Fire off an intent to check if a TTS engine is installed
        Intent checkIntent = new Intent();
        checkIntent.setAction(TextToSpeech.Engine.ACTION_CHECK_TTS_DATA);
        startActivityForResult(checkIntent, MY_DATA_CHECK_CODE);

    }

    /**
     * Executed when a new TTS is instantiated. Some static text is spoken via TTS here.
     * @param i
     */
    public void onInit(int i)
    {
        mTts.speak("Hello folks, welcome to my little demo on Text To Speech.",
                TextToSpeech.QUEUE_FLUSH,  // Drop all pending entries in the playback queue.
                null);
    }


    /**
     * This is the callback from the TTS engine check, if a TTS is installed we
     * create a new TTS instance (which in turn calls onInit), if not then we will
     * create an intent to go off and install a TTS engine
     * @param requestCode int Request code returned from the check for TTS engine.
     * @param resultCode int Result code returned from the check for TTS engine.
     * @param data Intent Intent returned from the TTS check.
     */
    public void onActivityResult(int requestCode, int resultCode, Intent data)
    {
        if (requestCode == MY_DATA_CHECK_CODE)
        {
            if (resultCode == TextToSpeech.Engine.CHECK_VOICE_DATA_PASS)
            {
                // success, create the TTS instance
                mTts = new TextToSpeech(this, this);
            }
            else
            {
                // missing data, install it
                Intent installIntent = new Intent();
                installIntent.setAction(
                        TextToSpeech.Engine.ACTION_INSTALL_TTS_DATA);
                startActivity(installIntent);
            }
        }
    }

    /**
     * Be kind, once you've finished with the TTS engine, shut it down so other
     * applications can use it without us interfering with it :)
     */
    @Override
    public void onDestroy()
    {
        // Don't forget to shutdown!
        if (mTts != null)
        {
            mTts.stop();
            mTts.shutdown();
        }
        super.onDestroy();
    }
}

```

Its really simple, what we do in the onCreate method is to fire off an intent to check if there is a valid TTS engine installed on the device. Think of it this way, if the device doesn’t have an engine which it can use for converting text to speech, how can this work? We use this check to see if we need to direct the user to download one.

The callback on this check is the onActivityResult, which will be invoked after the check has complete. In this method we first check the MY\_DATA\_CHECK\_CODE, this value can be anything you like, I’ve just chosen 1234. This is basically a checksum to check that the intent we receive in the onActivityResult is the one we’re expecting.

After that we check the result code, if it is CHECK\_VOICE\_DATA\_PASS that means there is a valid TTS engine available, so we can create a TextToSpeech instance. If we don’t get a CHECK\_VOICE\_DATA\_PASS then we assume there is either no TTS engine, or the one we have doesn’t work properly, so we create another intent that directs the user off to install one.

If the above was a success, then by creating a new TextToSpeech object we’ll invoke the onInit method, this is quite simple all it does is call the speak method on the TextToSpeech object and passes it a static String to speak.

The QUEUE_FLUSH is a nifty little feature which flushes out anything else the engine is trying to speak.

Checkout the [branch I’ve created on Github](https://github.com/jameselsey/TextToSpeechDemo/tree/TextToSpeechDemoStaticSpeech), you can download the complete source code for this and have a play.

Thats all nice and dandy, but having a static String to be spoken is a little boring, lets have a look at how we can modify this so that you can have a text box, type in some text and hit a button to have it spoken. Have a look at the following Activity, its similar to the above yet improved to do dynamic speech.

```java
/\*\*  
 \* Author : James Elsey  
 \* Date : 26/Feb/2011  
 \* Title : TextToSpeechDemo  
 \* URL : Http://www.JamesElsey.co.uk  
 \*/  
package com.jameselsey.demo.texttospeechdemo;

import android.app.Activity;  
import android.content.Intent;  
import android.os.Bundle;  
import android.speech.tts.TextToSpeech;  
import android.view.View;  
import android.widget.EditText;

/\*\*  
 \* This class demonstrates checking for a TTS engine, and if one is  
 \* available it will spit out some speak based on what is in the  
 \* text field.  
 \*/  
public class Main extends Activity implements TextToSpeech.OnInitListener  
{  
 private TextToSpeech mTts;  
 // This code can be any value you want, its just a checksum.  
 private static final int MY\_DATA\_CHECK\_CODE = 1234;

 /\*\*  
 \* Called when the activity is first created.  
 \*/  
 @Override  
 public void onCreate(Bundle savedInstanceState)  
 {  
 super.onCreate(savedInstanceState);  
 setContentView(R.layout.main);

 // Fire off an intent to check if a TTS engine is installed  
 Intent checkIntent = new Intent();  
 checkIntent.setAction(TextToSpeech.Engine.ACTION\_CHECK\_TTS\_DATA);  
 startActivityForResult(checkIntent, MY\_DATA\_CHECK\_CODE);

 }

 /\*\*  
 \* This method is bound to the speak button so is invoked anytime that is clicked.  
 \* @param v View default view.  
 \*/  
 public void speakClicked(View v)  
 {  
 // grab the contents of the text box.  
 EditText editText = (EditText) findViewById(R.id.inputText);

 mTts.speak(editText.getText().toString(),  
 TextToSpeech.QUEUE\_FLUSH, // Drop all pending entries in the playback queue.  
 null);  
 }  
 /\*\*  
 \* Executed when a new TTS is instantiated. We don’t do anything here since  
 \* our speech is now determine by the button click  
 \* @param i  
 \*/  
 public void onInit(int i)  
 {

 }

 /\*\*  
 \* This is the callback from the TTS engine check, if a TTS is installed we  
 \* create a new TTS instance (which in turn calls onInit), if not then we will  
 \* create an intent to go off and install a TTS engine  
 \* @param requestCode int Request code returned from the check for TTS engine.  
 \* @param resultCode int Result code returned from the check for TTS engine.  
 \* @param data Intent Intent returned from the TTS check.  
 \*/  
 public void onActivityResult(int requestCode, int resultCode, Intent data)  
 {  
 if (requestCode == MY\_DATA\_CHECK\_CODE)  
 {  
 if (resultCode == TextToSpeech.Engine.CHECK\_VOICE\_DATA\_PASS)  
 {  
 // success, create the TTS instance  
 mTts = new TextToSpeech(this, this);  
 }  
 else  
 {  
 // missing data, install it  
 Intent installIntent = new Intent();  
 installIntent.setAction(  
 TextToSpeech.Engine.ACTION\_INSTALL\_TTS\_DATA);  
 startActivity(installIntent);  
 }  
 }  
 }

 /\*\*  
 \* Be kind, once you’ve finished with the TTS engine, shut it down so other  
 \* applications can use it without us interfering with it :)  
 \*/  
 @Override  
 public void onDestroy()  
 {  
 // Don’t forget to shutdown!  
 if (mTts != null)  
 {  
 mTts.stop();  
 mTts.shutdown();  
 }  
 super.onDestroy();  
 }  
}
```

The main changes here is removing the code from the onInit method, we do this because when we create the TTS object we don’t actually know at that point what text we want spoken, so we leave it blank.

Another change is the introduction of the speakClicked method, there is a button which is bound to this method, so when that button is clicked this method is invoked (I wrote [another tutorial](/binding-your-buttons-to-your-activity-the-easy-way) on that, please check it out).

So when the button is clicked, we’ll take whatever text is in the EditText and have that spoken, simple as that!

I’ve put all this in a [separate branch on my Github](https://github.com/jameselsey/TextToSpeechDemo/tree/TextToSpeechDemoDynamicSpeech) so feel free to check it out.

Lastly, don’t forget to stop and shutdown the TTS object when you’re done, you don’t want it causing issues for other applicaitons that may want to use the TTS engine.

Further reading
---------------

- [Github source code for the static speech demo](https://github.com/jameselsey/TextToSpeechDemo/tree/TextToSpeechDemoStaticSpeech)
- [Github source code for the dynamic speech demo](https://github.com/jameselsey/TextToSpeechDemo/tree/TextToSpeechDemoDynamicSpeech)
- [Google documentation for text to speech](http://developer.android.com/resources/articles/tts.html)