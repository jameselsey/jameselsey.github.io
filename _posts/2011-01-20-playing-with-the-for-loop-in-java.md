---
title: 'Playing with the For loop in Java'
date: '2011-01-20T22:04:07+10:00'
permalink: /playing-with-the-for-loop-in-java
author: 'James Elsey'
category:
    - Java
tag:
    - for
    - java
    - loop
    - OCJP
    - ocp
    - OCPJP
    - scjp
---
The for loop is an extremely flexible and powerful way of iterating over a code block for a set number of times. Generally speaking, this type of loop is great for situations when you need to repeat code for a definitive number of times, for example you know that your shopping basket contains 10 items, so you need to repeat the loop body 10 times, to iterate through each item and print it onto the screen.

In order to use this loop, you must first create the for loop, and then specify the code block that is used for iteration. Lets take a look at a brief bit of psuedo code.

```
for(initialization; Boolean_expression; update)
{
   //Statements
}
```

So there are 3 things that happen inside the for loop delcaration, lets have a look at those too.

- **Initialisation** – This is where you can setup any control variables for the loop, normally you would set up an int to hold the position you have in the loop. Technically this part isn’t required, you can just leave it blank, but most of the time you would use it.
- **Boolean expression** – This boolean expression is evaluated before each cycle of the loop to determine if another iteration should be performed. Before the body of the loop is executed, this expression is evaluated, if it equals true, then the loop body executes, if it is false, then it goes to the next line of code after the loop body.
- **Update** – After the body of the loop has execute, the update section will occur. This allows you to update any of the control variables that were setup in the initialisation block. This part isn’t mandatory, so you could just leave the semi colon.

Enough talk, lets actually have a look at a real life for loop.

```java
public class MonkeySniffer
{
    public static void main(String[] args)
    {
         for (int i = 0; i < 10; i++)
         {
             System.out.println(i);
         }
         System.out.println("Done with the loop");
    }
}
```

Just before we get into what actually happens, you might be thinking that the “for” line looks a little complex, so how do you know where the initialisation ends and the boolean expression starts? Easy, look for the semi-colon. The semi-colon is the delimiter between the 3 sections.

So lets think about this above example a little more. We can see that during the initialisation block we create a new int called i, and assign it the value of 0. Thats our control variable. Next the boolean expression, before each iteration of the loop we will check to make sure that i is smaller than 10. If i is less than 10, we’ll run the loop body, otherwise we’ll continue to the next line of code after the loop. Since we have just initialised i to be 0, it is of course less than 10 so we run the code body.

The code body will simply output the value of i to the system console. After the code body is complete, we run the update section of the loop, in this case we increment i. At this stage i would then become 1.

That is just one iteration, we then attempt another. Remember from earlier, that we only run the initialisation once. We do this only once, otherwise we would then reset i back to 0 and the loop would never get anywhere. So for the second iteration we evaluate the expression again, in our case we just set i to be 1, so it still matches the expression, and we continue into the code body.

This happens over and over again, until we get to a stage where i isn’t smaller than 10, its actually 11. The boolean expression fails, so we don’t enter the loop body, we’re done, finished, fín, finito. We move onto the next line, being a print of “Done with the loop”.

Lets have a look at another example, this time we’ll count downwards instead of upwards :

```java
public class MonkeySniffer
{
    public static void main(String[] args)
    {
        for (int i = 10; i > 0; i--)
        {
            System.out.println(i);
        }
        System.out.println("Done with the loop");
    }
}
```

This time, we start off “i” as 10. Then we loop through while “i” is greater than 0. After each iteration we decrement i by 1. So in plain English, we start off at 10, and count downwards by 1 until we reach 0.

Getting the hang of it? Well lets throw some gas on the fire, and count both ways, at the same time :

```java
public class MonkeySniffer
{
    public static void main(String[] args)
    {
        for (int i = 0, j = 10; i < 10 && j > 0; i++, j--)
        {
            System.out.println("i is " + i + " and j is " + j);
        }
        System.out.println("Done with the loop");
    }
}
```

So what we are doing here, is setting up i as 0, and j as 10. Then while both i is less than 10, and j is greater than 0, continue with the loop. After each iteration, increment i, and decrement j. During each iteration we print out the current value of i and j.

Avoid the continuous loop..

As I said earlier in the post, the statements inside the for loop declaration are not mandatory, you could choose not to provide them. Since you don’t provide a boolean expression, the loop will never know when to exit, so will continue over and over again, have a look at the following :

```java
public class MonkeySniffer
{
    public static void main(String[] args)
    {
        for (;;)
        {
            System.out.println("Someone make me a cup of tea!");
        }
        // the below is unreachable, since the above is a continuous loop
        // System.out.println("Done with the loop");
    }
}
```

Of course, its not very pretty, but it does the job. There may be times when you do need to keep repeating something continuosly, but personally the following option is far cleaner :

```java
public class MonkeySniffer
{
    public static void main(String[] args)
    {
        while (true)
        {
            System.out.println("Someone make me a cup of tea!");
        }
        // the below is unreachable, since the above is a continuous loop
        // System.out.println("Done with the loop");
    }
}
```

Further Reading  
[Oracle Tutorial on the for loop](http://download.oracle.com/javase/tutorial/java/nutsandbolts/for.html)