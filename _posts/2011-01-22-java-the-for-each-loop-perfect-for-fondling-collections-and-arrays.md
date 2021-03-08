---
title: 'Java; the for-each loop, perfect for fondling collections and arrays'
date: '2011-01-22T14:54:15+10:00'
permalink: /java-the-for-each-loop-perfect-for-fondling-collections-and-arrays
author: 'James Elsey'
category:
    - Java
tag:
    - enhanced-for
    - for
    - java
    - loop
    - OCJP
    - OCPJP
    - scjp

---
The for loop is great, but is it really that nice when you want to iterate over an array or collection? You’d have to do something like the following :

```java
import java.util.ArrayList;
import java.util.List;

public class MonkeySniffer
{
    public static void main(String[] args)
    {
        List<String> myList = new ArrayList<String>();
        myList.add("Hello");
        myList.add("James");
        myList.add("Elsey");

        for (int i = 0; i < myList.size(); i++)
        {
            System.out.println(myList.get(i));
        }
    }
}
```

Works fine doesn’t it? Looks a bit messy doesn’t it? No need for the index since we only ever use it to get the current value out of the list. Lets have a look at the [for-each](http://download.oracle.com/javase/1.5.0/docs/guide/language/foreach.html) loop (often called the enhanced for loop) and how it can help

```java
for (String s : myStrings)
{
   // do stuff with "s"
}
```

Essentially, what the for each loop does, is allow you to loop through each item in the collection or array. The above example, in plain English means : “for each String in myStrings, assign it to s and do something with it”.

Now lets look at a real example

```java
public class MonkeySniffer
{
    public static void main(String[] args)
    {
        List<String> myList = new ArrayList<String>();
        myList.add("Hello");
        myList.add("James");
        myList.add("Elsey");

        for (String s : myList)
        {
            System.out.println(s);
        }
    }
}
```

Thats great! How much cleaner and easier to read is that?! Since there is no boolean expression involved with this type of looping, it is always assumed that the loop will iterate through every item in the array or collection.

Lets have a look at another few examples to get your understanding up to scratch

```java
import java.util.ArrayList;
import java.util.List;

public class MonkeySniffer
{
    public static void main(String[] args)
    {
        List<String> myList = new ArrayList<String>();
        myList.add("Hello");
        myList.add("James");
        myList.add("Elsey");
        for (String s : myList)
        {
            System.out.println(s);
        }
        
        char[] myChars = new char[]{'a','b','c'};
        for (char c : myChars)
        {
            System.out.println("char is : " + c);
        }
        
        int[] myInts = new int[]{1,2,3,4};
        for (int i : myInts)
        {
            System.out.println("int is : " + i);   
        }
    }
}
```

I hope I’ve explained it well, if I can improve, please let me know!

Happy looping!