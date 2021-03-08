---
title: 'Incrementing and Decrementing, the operators'
date: '2010-09-21T22:49:13+10:00'
permalink: /incrementing-and-decrementing-the-operators
author: 'James Elsey'
category:
    - Java
tag:
    - 'core java'
    - decrementor
    - increment
    - incrementor
    - iterator
    - java
---
The operators for incrementing and decrementing in Java has been irritating me for some time now, just when I think I understand whats going on, I try a mock exam for SCJP and I get caught out. So I’ve decided to sit down, do a bit of reading, and drill it out.

Take this example, what do you think the output will be?

```java
int a = 1;
int b = 5;
System.out.print("a++ is : " + a++);
System.out.print("++b is : " + ++b);
```

The output will be :

1  
6

Right heres the explanation. We have two things going on here, we have a Postfix(b++) operator and a Prefix(++b) operator.

What this means, in plain and simplest English is as follows

a++ means, use the value of a in the executing statement, THEN increment it

++b means, BEFORE we use the value of b, increment it

So there you have it, if the operator comes before the variable (Prefix), it means do the incrementing/decrementing before you evaluate the expression in question, and if the operator comes after (Postfix), then use the current value of the variable, and when thats done, increment it (maybe for use next time, such as in a loop?)

The same works for the decrementing operator, such as –a or a–

Simples