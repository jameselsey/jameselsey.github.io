---
title: 'Asserting for a Toast message using Robolectric'
date: '2012-02-18T03:02:01+10:00'
permalink: /asserting-for-a-toast-message-using-robolectric
author: 'James Elsey'
category:
    - Android
    - Java
tag:
    - android
    - robolectric
---
I’ve recently put together a few proof of concept applications, and since they’re “rough and ready” applications, a lot of the functionality is actually mocked, or given a stubbed implementation.

For example, I’ve got various buttons for things like “sign in with LinkedIn” or “Connect with Facebook”. Since its just a proof of concept, and those features aren’t really must haves, I stub them with a [Toast ](http://developer.android.com/guide/topics/ui/notifiers/toasts.html)message saying something along the lines of “feature not yet implemented”.

![toast](/assets/post_images/2012/toast.jpg)

This gives the user the opinion that the buttons are there, functional, however the actions they take are not yet implemented.

This is fine, however if you have numerous stubbed toast messages across the application, or even real toast messages that you have in production standard code, you’ll want to unit test those to be sure you don’t introduce defects when you’re busy beavering away in other areas of the source code.

Fortunately we can use the [Robolectric ](http://pivotal.github.com/robolectric/)framework to cover a lot of these unit tests. I won’t go into detail about [Robolectric ](http://pivotal.github.com/robolectric/)here, so please head over to their site and have a look at the [basics](http://pivotal.github.com/robolectric/user-guide.html) before you can understand this post.

The assertion is pretty straight forward, and consists of the following :

```java
@Test
public void assertValidationFailureWithNullInput()
{
	searchEditText.setText(null);
	searchButton.performClick();

	ShadowHandler.idleMainLooper();
	assertThat( ShadowToast.getTextOfLatestToast(), equalTo("Please enter a value."));
}
```

What I have here, is an EditText that I use for a search button, I set its value to null and then click on it. The purpose of the test is to ensure that submitting “search” with a null input will be caught by my validation, and a [toast ](http://developer.android.com/guide/topics/ui/notifiers/toasts.html)message prompting the user to enter valid data.

Hope that helps, and if you have any questions please leave a comment :)