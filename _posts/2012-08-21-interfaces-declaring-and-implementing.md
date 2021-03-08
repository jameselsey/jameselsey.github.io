---
title: 'Interfaces, declaring and implementing'
date: '2012-08-21T20:12:58+10:00'
permalink: /interfaces-declaring-and-implementing
author: 'James Elsey'
category:
    - Java
tag:
    - OCPJP
    - scjp
---
Another bite-size SCJP blog post, this time we’ll tackle interfaces.

Interfaces are, in the simplest terms, just a contract. It specifies things that a class **must** do if that class signs up to its contract, but it doesn’t provide any details of **how** those things must be done.

For example, you may wish to create an interface called Driveable. This interface defines the behaviour of anything that wants to become driveable, whether it be a Car class, a Motorcycle class, or even a Boat class.

You can think of an interface like a 100% abstract class, whereby all methods are implicitly abstract and public. Any class that decides to implement the interface, but provide method bodies for every single method in that interface, otherwise the compiler will complain.

Have a look at my sample below of a really basic interface and its implementation, you can find these on my [SCJP samples](https://github.com/jameselsey/SCJP-OCPJP-SampleCode) on Github:

```java
package com.jameselsey.demo.scjp.declarations_initialisation_scoping;

/**
 * Author:  JElsey
 * Date:    20/08/2012
 *
 * A quick example to show how an interface can be created, and more importantly how it is implemented.
 */
public class ImplementingAnInterface
{
    public static void main(String[] args)
    {
        // Notice the polymorphism here once again, how car IS-A driveable
        Driveable d1 = new Car();
        Driveable d2 = new Motorcycle();
        // The implementations provide the drive() method body.
        d1.drive();
        d2.drive();
    }
}

/*
 Create an interface, along with its methods. Think of it like a contract, anything that wants to become Driveable MUST
 implement the drive method, otherwise the contract is not fulfilled and the compiler will complain!
  */
interface Driveable
{
    public void drive();
}

class Car implements Driveable
{
    @Override
    public void drive()
    {
        System.out.println("Super charged v8 goes VROOOOM!!");
    }
}
class Motorcycle implements Driveable
{
    @Override
    public void drive()
    {
        System.out.println("Front rears up, wheelie down the highway!");
    }
}

```