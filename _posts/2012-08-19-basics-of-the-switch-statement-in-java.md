---
title: 'Basics of the switch statement in Java'
date: '2012-08-19T18:37:34+10:00'
permalink: /basics-of-the-switch-statement-in-java
author: 'James Elsey'
category:
    - Java
tag:
    - case
    - certification
    - 'core java'
    - java
    - OCJP
    - OCPJP
    - scjp
    - switch

---
*If* statements are great, but sometimes they are just not very practical when you have to test for more than a handful of conditions, have a look at this very poor example of how you build up a long set of if-else statements, its fine for a few checks, but if you’re doing multiple equality checks on a value, its best to use a switch statement for flexibility and readability:

```java

public class PoorIfStatement
{
    public static void main(String[] args)
    {
        int myInt = 5;

        if (myInt == 1)
        {
            System.out.println(myInt);
        }
        else if (myInt == 2)
        {
            System.out.println(myInt);
        }
        else if (myInt == 3)
        {
            System.out.println(myInt);
        }
        else if (myInt == 4)
        {
            System.out.println(myInt);
        }
        else if (myInt == 5)
        {
            System.out.println(myInt);
        }
        else
        {
            System.out.println("Unexpected number...");
        }
    }
}

```

Now, lets have a look at the same example, but using a switch statement instead, looks cleaner huh?

```java
public class CaseStatements
{
    static int myInt ;

    public static void main(String[] args)
    {

        myInt = 2;

        switch(myInt)
        {
            case 1:
                System.out.println("first");
                break;
            case 2:
                System.out.println("second");
                break;
            case 3:
                System.out.println("third");
                break;
            case 4:
                System.out.println("fourth");
                break;
            case 5:
                System.out.println("fifth");
                break;
            default :
                System.out.println("Unexpected number...");
        }

    }
}

```

Before we grab our coats and get ready to run, there is a lot more to the switch statement than a mere alternative to a set of ifs, the switch statement can execute more than one case statement! The program will read down from the top of the switch block, and as soon as it finds a suitable match, it will drop in and start processing whatever it finds. Think of it a little bit like a penny machine, the program is looking down the switch block, trying to find one which its penny will fit in, once it does, it pushes the penny in and it drops all the way down. That’s a little strange analogy of it, but its a good one to remember.

It is important to note, that like the above example, if you put in a break statement, then the program will exit out at that point, and not execute any further statements, this is great for if you need to one case statement and not “this case statement and then everything else below me”.

A lot nicer to read isn’t it? And what is better, it is much more flexible. If you need to check for additional conditions, you just need to add a case statement and specify what to do.

Don’t forget, that you can use the default block to catch conditions that don’t specifically match any of the cases, typically you’d put this at the bottom.

Here is another set of examples, that are available up on my [SCJP code snippets](https://github.com/jameselsey/SCJP-OCPJP-SampleCode) on Github:

```java
package com.jameselsey.demo.scjp.flow_control;

/**
 * Author:  JElsey
 * Date:    07/08/2012
 *
 * Toying around with the Switch statement a little, running on Java 1.6 so not using switch on Strings yet.
 */
public class SwitchStatement
{
    enum Animals
    {
        CAT, DOG, FISH
    }

    public static void main(String... args)
    {
        switchOnInt(5);
        switchOnInt(0);
        switchOnInt(2);
        switchOnInt(3);

        switchOnChar('a');
        switchOnChar('b');
        switchOnChar('c');
        switchOnChar('d');
        switchOnChar('A');
        switchOnChar('g');

        switchOnEnum(Animals.CAT);
        switchOnEnum(Animals.DOG);
        switchOnEnum(Animals.FISH);
    }

    /**
     * This method switches on an int, notice the (1+2) which is evaluated to 3. Also note no appearance
     * of the break statement in the 3rd block, so default will be execute in addition to 3
     *
     * @param theInt int
     */
    public static void switchOnInt(int theInt)
    {
        System.out.println("\n\nAssessing int :" + theInt);
        switch (theInt)
        {
            case 1:
            {
                System.out.println("The int was 1");
                break;
            }
            case 2:
            {
                System.out.println("The int was 2");
                break;
            }
            case (1 + 2):
            {
                System.out.println("The int was 3, and we're not going to break here either");
                //break;
            }
            default:
            {
                System.out.println("This is the default");
            }
        }
    }

    public static void switchOnChar(char theChar)
    {
        System.out.println("\n\nAssessing char : " + theChar);
        switch (theChar)
        {
            case 'a':
            {
                System.out.println("The char was a");
                break;
            }
            case 'b':
            {
                System.out.println("The char was b");
                break;
            }
            case ('c' | 'd'):
            {
                System.out.println("The char bitwised to make g");
                break;
            }
            default:
            {
                System.out.println("The char didn't match anything, must be something else");
            }
        }
    }

    public static void switchOnEnum(Animals theAnimal)
    {
        System.out.println("\n\nAssessing Animal :" + theAnimal);
        switch (theAnimal)
        {
            case CAT:
            {
                System.out.println("The Animal was CAT");
                break;
            }
            case DOG:
            {
                System.out.println("The Animal was DOG");
                break;
            }
            default:
            {
                System.out.println("The animal was something other than CAT or DOG");
            }
        }
    }
}
```