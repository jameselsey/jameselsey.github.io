---
title: 'Varargs, for those times when you're not sure how many parameters you'll have'
date: '2011-09-01T18:23:37+10:00'
permalink: /varargs-for-those-times-when-youre-not-sure-how-many-parameters-youll-have
author: 'James Elsey'
category:
    - Java
tag:
    - certification
    - 'core java'
    - methods
    - OCJP
    - OCPJP
    - scjp
    - varargs

---
As of Java 5, methods are now able to accept from 0 to many arguments. Sounds confusing, but you could actually be using it already without knowing, how about looking at your main methods?

```java
public static void main(String... args)
```

As we can see above, var args are declared as TYPE… NAMEOFVARIABLE. Lets take a look at a basic example I’ve created :

```java
public class MethodsWithVarArgs
{
    public static void main(String... args)
    {
        // print out various numbers, just 'cos we can
        printNumbers();
        printNumbers(1);
        printNumbers(1, 2, 3, 4);

        // Print out information on various dice.
        printDieTypeAndItsNumbers("Die with no numbers on it");
        printDieTypeAndItsNumbers("Dodgy die that could never exist...", 1);
        printDieTypeAndItsNumbers("D6", 1, 2, 3, 4, 5, 6);
        printDieTypeAndItsNumbers("D9", 1, 2, 3, 4, 5, 6, 7, 8, 9);
    }

    public static void printNumbers(int... numbers)
    {
        for (int i : numbers)
        {
            System.out.println(i);
        }
        if (numbers.length == 0)
        {
            System.out.println("I didn't get passed in anything... :(");
        }
    }

    public static void printDieTypeAndItsNumbers(String dieType, int... numbersOnTheDie)
    {
        System.out.println("Die : " + dieType + " has the following numbers on it...");
        for (int numberOnDie : numbersOnTheDie)
        {
            System.out.println(numberOnDie);
        }
    }
}

```

As we can see in both methods, both accept a vararg input parameter. We can invoke these methods with no ints, or many ints, the methods are totally accepting of it!

In effect, the varargs are being treated as arrays, but we don’t have the hassle of getting our hands dirty creating the arrays, which is always a bonus.

One important thing to notice, is that you can only ever, EVER have one vararg parameter, and it must be the last one. The following are all invalid:

```java
public static void invalidBecauseMoreThanOneVarArg(String... first, String... second) {}
public static void invalidBecauseVarArgNotAtEnd(String... varargs, String message) {}
```

For the above, they are invalid, why? Think of it this way, if you had 2 vararg input parameters, how would you know where one ends, and the other begins?

So, feel free to use varargs, but make sure you only have one, and its at the end of all your input parameters.