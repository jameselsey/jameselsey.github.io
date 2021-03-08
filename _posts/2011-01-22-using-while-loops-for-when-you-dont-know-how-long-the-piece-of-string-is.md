---
title: 'Using while loops, for when you don't know how long the piece of string is'
date: '2011-01-22T14:12:55+10:00'
permalink: /using-while-loops-for-when-you-dont-know-how-long-the-piece-of-string-is
author: 'James Elsey'
category:
    - Java
tag:
    - java
    - loop
    - OCJP
    - scjp
    - while
    - 'while loop'
---
Just another bitesize SCJP post here, looking at the [while loop](http://download.oracle.com/javase/tutorial/java/nutsandbolts/while.html) in Java.

There may be times, where you need to continually iterate over a code block for an *unknown* number of times. What you can do, is to loop through a code block while a condition is true, something else might *change* that condition, and when this happens, you can exit out of the loop.

In a psuedo-code way, this is how the while loop operates :

```java
while (boolean_expression)
{
  //do stuff here
}
```

The boolean expression, as the name suggests must equal a boolean value. All of the following examples are valid

```java
public class MonkeySniffer
{
    public static void main(String[] args)
    {
        int x = 2;
        while (x == 2)
        {
            System.out.println("Inside the loop!");
            x = 3;
        }

        String myString = "James";
        while (myString.equals("James"))
        {
            System.out.println("Inside the loop!");
            myString = "Elsey";
        }

        while (3 > 2)
        {
            System.out.println("You'll only see this once, then we'll break out of the loop");
            break;
        }

        while (true)
        {
            System.out.println("You'll only see this once, then we'll break out of the loop");
            break;
        }

        System.out.println("Done with our looping samples");
    }
}
```

For the first 2 examples, we check to see if a variable is a certain value, if that is true (in which it is) then we drop into the loop and print out a value. For the first 2 examples, during the first iteration we actually modify that variable, so when the loop ends and we re-check the boolean expression it now evaluates to true, so we don’t drop in for another iteration.

The last 2 examples can be considered as continual, or never-ending loops, since the evaluates will always be true. For example, 3 will always be greater than 2, nothing except the laws of physics and maths can ever change that, true will always be true, always! This means that the loop will continually iterate over and over again, until the end of time or when you pull the plug. The only way to get out of this type of loop is to use the break keyword, this essentially bails out of the loop.

Lets have a look at one more example :

```java
public class MonkeySniffer  
{  
 public static void main(String\[\] args)  
 {  
 int x = 0;  
 while (x &lt; 10)  
 {  
 System.out.println("Inside the loop!");  
 x++;  
 }  
 }  
}
```

This behaviour is very similar to that of the standard [for loop](/playing-with-the-for-loop-in-java), a condition is met and the loop is entered, until that condition can no longer be met.

To be honest, there is very little to the while loop, so I think I’ve covered it all here! If theres anything I’ve missed, please leave me some comments!

Happy coding.