---
title: 'Android; Using intents to refresh tab content'
date: '2010-09-21T22:51:17+10:00'
permalink: /android-using-intents-to-refresh-tab-content
author: 'James Elsey'
category:
    - Android
    - Java
tag: []
---
As part of my [MeCercana ](/open-source-projects)application, I needed to refresh data in some form. My application has 3 tabs, one of the tabs displays contacts from the contacts book, this works great if you switch the phone off after adding a new contact, but that isn’t ideal.

What I needed, was a way to refresh, or re-query the data on demand, the best way to do this was to bind an intent onto the tab, so each time you click the tab icon it fires off that Intent and thus simulates a forced refresh.

This is how I setup my tab, I created an Activity called DefaultActivity, which acts as the tab container

```java
public void onCreate(Bundle savedInstanceState)
{
	    super.onCreate(savedInstanceState);
    	    setContentView(R.layout.main);

	    Resources res = getResources();
	    TabHost tabHost = getTabHost();
	    TabHost.TabSpec spec;
	    Intent intent;

	    // Create an Intent to launch an Activity for the tab (to be reused)
	    intent = new Intent().setClass(this, MyContactsActivity.class);
	    // Initialize a TabSpec for each tab and add it to the TabHost
	    spec = tabHost.newTabSpec("myContacts");
	    spec.setIndicator("", res.getDrawable(R.drawable.ic_tab_contacts));
	    //Adding a flag into the intent, will use this later
            intent.putExtra("RefreshContacts", true);
	    spec.setContent(intent);
	    tabHost.addTab(spec);

            // Repeat the above to add more tabs
}
```

Don’t worry too much about what the above does, I’ll explain that in another post, but as a quick heads up, it creates some tabs and assigns some layouts to them. An Intent is bound to a tab host, which basically means if you click the tab icon it will issue that Intent. This is particulary useful as we can put extra data onto the intent, and then use it at the other end

Right, now over to the target activity. So we’ve bound an intent onto the tab button, now lets see what happens.

```java
        @Override
	public void onCreate(Bundle savedInstanceState)
	{
	    super.onCreate(savedInstanceState);
	    drawUI();
	}

	/**
	 * This is called immediately after the onCreate() method is run, and also when an activity
	 * is started, such as startActivity(intent)
	 */
	@Override
	public void onStart()
	{
		super.onStart();
		drawUI();
	}

	/**
	 * Placeholder method to initialise data and UI
	 */
	public void drawUI()
	{
		setContentView(R.layout.contactsview);
                // Do stuff in here such as querying for contacts
        }
```

Firstly, the onCreate method is called, and in here we initialise anything that is important for the activity to run. Secondly, the onStart method will be called immediately after. Both of these use the common method of drawUI, which takes care of getting the data and putting it on screen.

The reason for this approach, is to firstly setup the data if it is the first time we are running this activity. Secondly the onStart is called when we start the activity (from the button) so therefore we need to run the drawUI method again to refresh the data.

Simple! Any questions/comments/suggestions, then please let me know!