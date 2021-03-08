---
title: 'How to send an email from your Android application'
date: '2011-04-16T16:38:48+10:00'
permalink: /how-to-send-an-email-from-your-android-application
author: 'James Elsey'
category:
    - Android
tag:
    - android
    - contact
    - email
    - gmail
---
If you’re like me, you’ll certainly like to hear feedback on your applications out there on the market, and what better method than via a direct email link.

I usually opt for a “contact me” button, which when clicked will open up the phones email client with a new email ready. The mail-to and subject will already be set so all the user needs to do is to type in their message and send. Lets have a look at setting this up in your application.

![Gmail-Android-update-compose](/assets/post_images/2011/Gmail-Android-update-compose.jpg)

First thing you’ll need is the button, so go ahead and add this whereever you want the button :

```xml
<Button
                android:layout_height="wrap_content"
                android:layout_width="fill_parent"
                android:text="@string/linkEmail"
                android:onClick="linkEmailClicked"
                android:singleLine="true"/>
```

Take note of the onClick property, we’ll need that in a moment. Now either create a new activity, or add the following method into an existing activity :

```java
/**
     * This method will be invoked when the user clicks on "email me" link
     * in the About the developer page, which will then spawn an email to
     * me
     * @param v View Default view
     */
    public void linkEmailClicked(View v)
    {
        Intent it = new Intent(Intent.ACTION_SEND);
        String[] tos = {getString(R.string.emailAddress)};
        it.putExtra(Intent.EXTRA_EMAIL, tos);
        it.putExtra(Intent.EXTRA_SUBJECT, getString(R.string.emailSubject));
        it.setType("text/plain");
        startActivity(it);
    }
```

Notice how the method name matches the onClick property in the XML? That’s how android works out what to invoke when the button is clicked. All the above code samples are from production code, so they should work fine for you!

This is incredibly simple, I don’t need to explain much here, other than storing my email address and email subject in the strings.xml file for ease of configuration.

Further Reading
---------------

- [Action_Send intent, the method of sending data](http://developer.android.com/reference/android/content/Intent.html#ACTION_SEND)
- [AndroidSam ](https://market.android.com/details?id=com.jameselsey.apps.androidsam&feature=search_result)– Download my app which has this feature in it, to see how it works!