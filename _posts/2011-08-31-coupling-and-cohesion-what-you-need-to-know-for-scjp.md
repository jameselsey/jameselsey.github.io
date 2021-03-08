---
title: 'Coupling and Cohesion, what you need to know for SCJP&#8230;'
date: '2011-08-31T18:40:23+10:00'
permalink: /coupling-and-cohesion-what-you-need-to-know-for-scjp
author: 'James Elsey'
category:
    - Java
tag:
    - cohesion
    - coupling
    - java
    - 'object oriented'
    - programming
    - scjp

---
Coupling and cohesion are two terms that often get mixed up, however they are actually really simple concepts, and the bonus is, they don’t just apply to Java.

Coupling
--------

Coupling, in its purest terms, means “the degree to which one class knows about another class”. If one class uses another class, that is coupling. Coupling is everywhere, but the level of coupling varies.

Consider the following example of a tightly coupled set of classes:

```java
public class TightlyCoupledClass
{
    public static void main(String[] args)
    {
        ExampleClass exampleClass = new ExampleClass();
        exampleClass.message += "James";
        System.out.println(exampleClass.message);
    }
}

class ExampleClass
{
    public String message = "hello ";
}
```

TightlyCoupledClassuses ExampleClass, therefore there is some coupling. The level of coupling is high as TightlyCoupledClassdirectly alters the message variable on ExampleClass. It’ll compile and run perfectly fine, but if you slip this little nugget into production code, you’re colleagues will bash you for eternity, because its poor design.

Think of it this way, what if ExampleClass was used in hundreds of classes, or what if ExampleClass was maintained by someone else; what would happen if they decide to change the scope of message, or to change its data type? It’ll break anything that uses it, since it is tightly coupled.

From the perspective of TightlyCoupledClass, it needs to get a message, and set a message, it should not care about how ExampleClass maintains that. When a class knows too much about another classes’ implementation, that is also tight coupling.

What we need, is encapsulation, this is another OO concept that shields a classes state and exposes it with accessor methods, meaning that a class can still be coupled, but loosely.

You should, be aiming for loosely coupled classes, lets re-visit the above sample but this time we’ll move from a tightly coupled set of classes, to a loosely coupled set of classes, using encapsulation.

```java
public class TightlyCoupledClass
{
    public static void main(String[] args)
    {
        ExampleClass exampleClass = new ExampleClass();
        String ourUpdatedMessage = exampleClass.getMessage() + "James";
        exampleClass.setMessage(ourUpdatedMessage);
        System.out.println(exampleClass.getMessage());
    }
}

class ExampleClass
{
    private String message = "hello ";

    public String getMessage()
    {
        return message;
    }

    public void setMessage(String message)
    {
        this.message = message;
    }
}
```

Cohesion
--------

Cohesion is another OO concept like coupling, however cohesion refers to the degree in which a class has a single, well defined purpose. If you had a class called Car, it would maintain state such as numberOfWindows, numberOfWheels, and have behaviour (methods) such as drive(), brake(), stop() etc. That would indicate that the class is cohesive, meaning that it serves a single, defined purpose.

Now, exploring a little, if the Car class suddenly decided to have methods such as switchOnTheTV(), or hangTheWashingOut(), then its not really doing Car-like behaviour. If a class is trying to do more than one thing, it is considered a low cohesive class.

Consider the following, highly cohesive class

```java
package ooconcepts;

public class Car
{
    private String make = "Aston Martin";
    private int numberOfWindows = 5;
    
    public void drive()
    {
        // do some driving!
    }
    
    public void brake()
    {
        // slow down!
    }
    
    public void stop()
    {
        // STOP!!!!
    }      
}

```

Conclusion
----------

These are the important bullet points to take away from this page:

- Coupling refers to the degree in which one class knows about another, or makes use of another classes’ members
- Loose coupling is GOOD as classes are well encapsulated to minimise references to each other
- Tight coupling is BAD because a class shouldn’t know about the workings of another
- Cohesion refers to the degree in which a class has a single, well defined purpose
- High cohesion is GOOD as a class does only what it should
- Low cohesion is BAD because a class is trying to solve everything and not just its specific purposes

Further Reading
---------------

1. [Why couplig is always bad](http://en.wikipedia.org/wiki/Coupling_%28computer_programming%29>Wikipedia%20entry%20for%20Coupling</a></li>%0A<li><a%20href=)
2. [Java Boutique](http://javaboutique.internet.com/tutorials/coupcoh/)