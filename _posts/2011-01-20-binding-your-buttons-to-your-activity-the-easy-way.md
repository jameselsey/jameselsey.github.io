---
title: 'Binding your buttons to your activity, the easy way!'
date: '2011-01-20T21:31:52+10:00'
permalink: /binding-your-buttons-to-your-activity-the-easy-way
author: 'James Elsey'
category:
    - Android
    - Java
tag:
    - android
    - button
    - java
    - method
    - onClick

---
OK, so you have several [buttons ](http://developer.android.com/reference/android/widget/Button.html)on your view, all linked into the same activity. Sounds simple enough, you probably have something that looks like the following :

```java
package com.jameselsey.buttons;

import android.app.Activity;
import android.os.Bundle;
import android.view.View;

import android.widget.Button;
import android.widget.Toast;

public class ButtonsActivity extends Activity
{

    @Override
    public void onCreate(Bundle savedInstanceState)
    {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);

        final Button button = (Button) findViewById(R.id.myButton1);
        button.setOnClickListener(new View.OnClickListener()
        {
            public void onClick(View v)
            {
                // Perform action on clicks
                Toast.makeText(ButtonsActivity.this, "Hello!", Toast.LENGTH_SHORT).show();
            }
        });

        final Button button2 = (Button) findViewById(R.id.myButton2);
        button.setOnClickListener(new View.OnClickListener()
        {
            public void onClick(View v)
            {
                // Perform action on clicks
                Toast.makeText(ButtonsActivity.this, "Hello Again!", Toast.LENGTH_SHORT).show();
            }
        });

    }

}
```

That works fine, however if you start adding more and more buttons, it’ll become quite messy. I mean seriously, do you really want to be declaring your buttons inside the onCreate method? Apart from making this method quite large in size (a potential maintenance nightmare), you’re also slightly boxing yourself in when it comes to making your code unit-testable. But fear not, I’ll let you into the secret of the “onClick” method on the button class.

Lets add a few buttons into our config xml file, such as the following :

```xml
<Button android:id="@+id/myButton1"
            android:onClick="myButtonPressed1"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:padding="10dp"
            android:text="Click me! 1"
            />
    <Button android:id="@+id/myButton2"
            android:onClick="myButtonPressed2"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:padding="10dp"
            android:text="Click me! 2"
            />
```

Notice the use of android:onClick, now update your activity to look something like the following. You’ll notice that the value contained in the android:onClick is actually the method name in your activity.

```java
package com.jameselsey.buttons;

import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.Toast;

public class ButtonsActivity extends Activity
{

    @Override
    public void onCreate(Bundle savedInstanceState)
    {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
    }

    public void myButtonPressed1(View v)
    {
        Toast.makeText(this, "Hello!", Toast.LENGTH_LONG).show();
    }

    public void myButtonPressed2(View v)
    {
        Toast.makeText(this, "Hello again!", Toast.LENGTH_LONG).show();
    }

}
```

This way of binding buttons is a lot cleaner, and helps to remove the button declaration from the Android lifecycle methods in your class. Give it a try, its one of those smaller features of the Android SDK but makes life a great deal more pleasant!

Happy coding