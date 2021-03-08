---
title: 'Android and SQLite, a REALLY easy tutorial that anyone can do'
date: '2011-02-26T18:46:30+10:00'
permalink: /android-and-sqlite-a-really-easy-tutorial-that-anyone-can-do
author: 'James Elsey'
category:
    - Android
    - Java
tag:
    - android
    - database
    - sql
    - sqlite

---
Howdy folks! Time for some more android fun. I’ve been meaning to explore some of the SQLite functionality for some time, as I require it in my Cerca de mí application, so heres a real quick and dirty overview of how to get going.

I know you want some source code to lift, but lets just have a quick chat about how this ol’ chesnut works.

Think of it this way, you’ve created some application on Android, and young Johnny downloads your game and plays it. He kills the arch-enemy Galron in under 2 minutes flat and he wants to save his score to brag to his chums about, how does that work in Android terms? Well, one option is to create a SQLite database for your application and use that to store application data such as high scores, usernames, perhaps even data required for your applications logic? (maybe some cool new items after killing that boss?).

Enough talk, create a new android project in the IDE of your choice and copy in the following into src/com/jameselsey/Main.java

```java
//   Quick and dirty SQLite example for Android
 
package com.jameselsey;

import android.app.ListActivity;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteException;
import android.os.Bundle;
import android.util.Log;
import android.widget.ArrayAdapter;
import java.util.ArrayList;
import java.util.List;

public class Main extends ListActivity
{

    private static String SAMPLE_TABLE_NAME = "PERSONS_TABLE";
    private SQLiteDatabase sampleDB = null;
    private List<String> results = new ArrayList<String>();
    private Cursor cursor = null;

    @Override
    public void onCreate(Bundle savedInstanceState)
    {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);

        try
        {
            sampleDB = openOrCreateDatabase("NAME", MODE_PRIVATE, null);
            createTable();
            insertData();
            lookupData();
            this.setListAdapter(new ArrayAdapter<String>(this, android.R.layout.simple_list_item_1,results));
        }
        catch (SQLiteException se)
        {
            Log.e(getClass().getSimpleName(), "Could not create or Open the database");
        }
        finally
        {

            if (sampleDB != null)
                sampleDB.execSQL("DELETE FROM " + SAMPLE_TABLE_NAME);
            sampleDB.close();
        }

    }

    /**
     * Create a table if it doesn't already exist
     */
    private void createTable()
    {
       sampleDB.execSQL("CREATE TABLE IF NOT EXISTS " +
                    SAMPLE_TABLE_NAME +
                    " (PERSON_NAME VARCHAR, " +
                    "  COUNTRY VARCHAR, " +
                    "  AGE INT(3));");
    }

    /**
     * Insert some test data, modify as you see fit
     */
    private void insertData()
    {
        sampleDB.execSQL("INSERT INTO " + SAMPLE_TABLE_NAME + " Values ('James','ENGLAND',25);");
        sampleDB.execSQL("INSERT INTO " + SAMPLE_TABLE_NAME + " Values ('Dave','USA',18);");
        sampleDB.execSQL("INSERT INTO " + SAMPLE_TABLE_NAME + " Values ('Jean-Paul','FRANCE',33);");
        sampleDB.execSQL("INSERT INTO " + SAMPLE_TABLE_NAME + " Values ('Sergio','SPAIN',42);");
        sampleDB.execSQL("INSERT INTO " + SAMPLE_TABLE_NAME + " Values ('Hitori','JAPAN',73);");
    }

    /**
     * Run a query to get some data, then add it to a List and format as you require
     */
    private void lookupData()
    {
        cursor = sampleDB.rawQuery("SELECT PERSON_NAME, COUNTRY, AGE FROM " +
                SAMPLE_TABLE_NAME +
                " where AGE > 10 ", null);

        if (cursor != null)
        {
            if (cursor.moveToFirst())
            {
                do
                {
                    String personName = cursor.getString(cursor.getColumnIndex("PERSON_NAME"));
                    String country = cursor.getString(cursor.getColumnIndex("COUNTRY"));
                    int age = cursor.getInt(cursor.getColumnIndex("AGE"));
                    results.add("" + personName + ", " + country + ", " + age);
                } while (cursor.moveToNext());
            }
            cursor.close();
        }
    }
}
```

This is the whole tutorial pretty much in one class. What we do is call out to various private members in the onCreate method. Firstly we need to create the table to store those highscores in (thats it Johnny hasn’t already played the game and created it already).

Next we’re going to insert some data, this is the key part. For this tutorial I’m happy inserting some static test data just to prove the point, but in your real life application you probably don’t want to do this, as you’ll be inserting data from other means.

Next we lookup the data, just a simple SQL select statement, nothing fancy here, but what we do is to create a List of results from that query, which we use to pass through an Adapter so it comes out the other end in a ListView.

That is pretty much it to be honest! I won’t post up AndroidManifest.xml or strings.xml since there are no changes there, but you might want the following res/layout/main.xml

```xml
<?xml version="1.0" encoding="utf-8" ?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android" android:orientation="vertical"
              android:layout_width="fill_parent" android:layout_height="fill_parent">
    <ListView android:id="@android:id/list" android:layout_width="fill_parent"
            android:layout_height="wrap_content" ></ListView>
</LinearLayout>
```

So there we have it, in under 5 minutes you can now create a SQLite database, insert some test data into it, then read it back and display on the screen. I’ll do some more in-depth SQLite tutorials in the future, please check back!

James