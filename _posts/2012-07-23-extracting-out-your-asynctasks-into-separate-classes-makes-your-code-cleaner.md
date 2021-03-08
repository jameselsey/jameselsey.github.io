---
title: 'Extracting out your AsyncTasks into separate classes makes your code cleaner'
date: '2012-07-23T21:12:17+10:00'
permalink: /extracting-out-your-asynctasks-into-separate-classes-makes-your-code-cleaner
author: 'James Elsey'
category:
    - Android
tag:
    - android
    - async-task
    - DRY
    - refactor
---
If you’re an android developer, chances are you’ve used an Async task more than once. As your apps develop, grow, and become more complex, theres a high chance you’re going to have multiple Async tasks. In an application I’ve recently been developing, I had around 5 Async tasks for a single activity, which made the actual activity quite difficult to read.

Adhering to KIS and DRY, I wanted to devise a mechanism for extracting the logic of these Async tasks out into separate external classes, thus reducing the clutter of inner classes in my activities, and also meaning that I could re-use these Async tasks elsewhere, and also externalising them makes them easier to mock and unit test.

The easiest mechanism I’ve found, is to use generics and provide a callback, which is somewhat similar to the delegate/protocol pattern in iOS programming.

Firstly, create a class with a generic method, like follows :

```java
/**
 * This is a useful callback mechanism so we can abstract our AsyncTasks out into separate, re-usable
 * and testable classes yet still retain a hook back into the calling activity. Basically, it'll make classes
 * cleaner and easier to unit test.
 *
 * @param <T>
 */
public interface AsyncTaskCompleteListener<T>
{
    /**
     * Invoked when the AsyncTask has completed its execution.
     * @param result The resulting object from the AsyncTask.
     */
    public void onTaskComplete(T result);
}
```

Then, in your activity you can reduce the inner class to just the following :

```java
public class FetchMyDataTaskCompleteListener implements AsyncTaskCompleteListener<MyPojo>
    {

        @Override
        public void onTaskComplete(MyPojo result)
        {
            // do something with the result
        }
    }
```

This means you can now create a separate class for the task, below is an example. Don’t forget to assign the callback listener in the constructor, so when the onPostExecute() happens you can invoke the callback to the onTaskComplete in your activity:

```java
public class FetchMyDataTask extends AsyncTask<String, Integer, MyPojo>
{
    private static final String TAG = "FetchMyDataTask";

    private Context context;
    private AsyncTaskCompleteListener<MyPojo> listener;

    public FetchVehicleTask(Context ctx, AsyncTaskCompleteListener<MyPojo> listener)
    {
        this.context = ctx;
        this.listener = listener;
    }

    protected void onPreExecute()
    {
        super.onPreExecute();
    }

    @Override
    protected MyPojo doInBackground(String... strings)
    {
        MyPojo myPojo = null;
        // do something with myPojo
        return myPojo;
    }

    @Override
    protected void onPostExecute(MyPojo myPojo)
    {
        super.onPostExecute(myPojo);
        listener.onTaskComplete(myPojo);
    }

```

All thats left to do, is to now “new up” the async task in your activity and start it. Don’t forget to initialise them with the callback listener, so use this to create the tasks in your activity:

```java
new FetchMyDataTask(this, new FetchMyDataTaskCompleteListener()).execute("InputString");
```

Thats it, it takes a little while to understand how this fits together, but its an incredibly flexible technique for tidying up your Async tasks, I’ve seen it used in a number of applications.

Thanks!