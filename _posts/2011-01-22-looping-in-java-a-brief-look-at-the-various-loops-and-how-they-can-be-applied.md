---
title: 'Looping in Java, a brief look at the various loops and how they can be applied'
date: '2011-01-22T14:57:18+10:00'
permalink: /looping-in-java-a-brief-look-at-the-various-loops-and-how-they-can-be-applied
author: 'James Elsey'
category:
    - Java
tag:
    - do
    - 'do while'
    - foreach
    - java
    - loop
    - loops
    - OCJP
    - OCPJP
    - scjp
    - while

---
One of the great features of any programming language is the ability to repeat blocks of code, sometimes indefinately, sometimes until a certain condition is met, or for a set number of iterations.

Luckily, Java comes with several flavours of loops, let have a brief look at our options

- [The “for” loop](/playing-with-the-for-loop-in-java) – The for loop is generally used when you know in advance how many iterations the loop must execute. The for loop enables you to setup a repeatable code block with access to the index(es)
- [The “for-each” loop (also known as the enhanced for loop)](/java-the-for-each-loop-perfect-for-fondling-collections-and-arrays) – Introduced in Java 5, the enhanced for loop is primarily used for iterating through arrays
- [The “while” loop](/using-while-loops-for-when-you-dont-know-how-long-the-piece-of-string-is) – A boolean expression is evaluated, if the outcome is true, then the code in the following block will be executed, and the expression will be evaluated again. This cycle will continue until the expression evaluates to false.
- [The “do-while” loop](/the-do-while-loop-always-executes-at-least-once) – This loop is very similar to the while loop. However, one drawback of the while loop is that it will only execute if the expression evaluates to true. What if you wanted to run the iteration always once to begin with, and then start checking for further iterations? Well we have some news, the do-while loop saves the day.

I’ve linked each of the above into a separate post, please have a look at those, I’ve tried to explain it as much as I can in accordance to the SCJP exam guidelines.

Happy coding!