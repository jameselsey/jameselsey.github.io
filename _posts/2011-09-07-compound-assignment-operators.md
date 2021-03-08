---
title: 'Compound Assignment Operators'
date: '2011-09-07T00:18:37+10:00'
permalink: /compound-assignment-operators
author: 'James Elsey'
category:
    - Java
tag:
    - certification
    - 'core java'
    - java
    - OCJP
    - OCPJP
    - scjp

---
There are various compound assignment operators, however it is only necessary to know the 4 basic compound assignment operators for the exam, being as follows:

1. += (Addition compound)
2. -= (Subtraction compound)
3. \*= (Multiplication compound)
4. /= (Division compound)

Essentially, they’re just a lazy way for developers to cut down on a few key strokes when typing their assignments. Consider the following examples that I’ve created to demonstrate this:

```java
package operators;

public class AssignmentsOperator
{
    public static void main(String[] args)
    {
        int x = 5;

        // This is the "longhand" way of adding 5 to x
        x = x + 5;
        // You could use the compound assingment as follows, to achieve the same outcome
        x += 5;


        // Lets look at the other 3...
        // This...
        x = x - 1;
        // ...is the same as
        x -= 1;

        // This...
        x = x * 10;
        // ...is the same as
        x *= 10;

        // This...
        x = x / 10;
        // ...is the same as
        x /= 10;

    }
}

```

That is it! Any suggestions, please let me know!

Happy coding.