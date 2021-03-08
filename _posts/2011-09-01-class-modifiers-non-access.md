---
title: 'Class Modifiers (non-access)'
date: '2011-09-01T19:46:00+10:00'
permalink: /class-modifiers-non-access
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
In addition to [class access modifiers](/class-access-modifiers), classes can also be marked with non-access modifiers. These modifiers imply rules on a class, but are not necessarily linked to access rights.

The following non-access modifiers are available:

- **final** – The class can’t be extended.
- **abstract** – The class has to be extended, and can’t be instantiated on its own.
- **strictfp** – Ensures that you always get exactly the same results from your floating point calculations on every platform your application runs on. If you don’t use strictfp, the JVM implementation is free to use extra precision where available.

Lets make things easier, its worth knowing what strictfp is, but its not on the SCJP exam so you don’t need to know how to use it, so you can almost brush that aside after remembering the information above.

Final classes can’t be extended / subclassed. Thats great, for those times when you don’t want people extending your classes, mark them private. Take the String class for example, that is private, as they didn’t want developers extending it and altering the way Strings behave, that would cause nightmares for all sorts of applications!

The other non-access modifier is the abstract modifier. There may be times when you don’t want people to instantiate your class. Say that you had a class Car, but you didn’t want people to start creating Car objects all over the place, you want them to extend your Car class and create their own functionality, you might choose to make Car abstract, like the following example:

```java
package declarations;

public abstract class Car
{
    public void doSomething()
    {
        
    }
}

class SportsCar extends Car
{
    public static void main(String[] args)
    {
        // do SportsCar stuff here
    }
}
```

Don’t forget, that a class can’t be both final and abstract, as the contradict each other, one can’t be subclassed, yet the other must be subclassed, it just won’t work!

We’ll touch more upon abstract classes in the interfaces sections, as interfaces are essentially 100% abstract classes, but more about that later :)