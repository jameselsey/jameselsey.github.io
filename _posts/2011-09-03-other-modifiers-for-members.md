---
title: 'Other modifiers, for members'
date: '2011-09-03T12:42:38+10:00'
permalink: /other-modifiers-for-members
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
We’ve already touched upon various modifiers, for classes (both [access ](/class-access-modifiers)and [non-access](/class-modifiers-non-access)), but there are also some more modifiers for members, as detailed here.

We have the following modifiers for members :

- **final** – Can’t be overridden
- **abstract** – No implementation specified, subclass must implement
- **synchronized** – Only a single thread of execution can pass through at a given time
- **native** – Implemented by a 3rd party piece of code (C++ for example)
- **strictfp** – Enforces foating point precision and doesn’t let the JVM do its own way

To make things easier, you don’t actually need to know how the [strictfp ](http://en.wikipedia.org/wiki/Strictfp)or [native ](http://en.wikipedia.org/wiki/Java_Native_Interface)work for the exam, but you’ll need to be aware of them. Strictfp guarantees precision on floating point calculations (otherwise the JVM implements precision however it feels), and the native is used for external implementations, such as implementing methods in C++.  
All you must remember with these, is that the native modifier applies only to methods, but the strictfp modifier applies to both classes and methods. So lets have a look at a brief example then pass aside on these two and focus on the ones that matter.

```java
package modifiers;

// The class can be marked as strictfp too!
public strictfp class NativeAndStrictfp
{
    // A normal method
    public String giveMeAString()
    {
        return "hello";
    }
    
    // strictfp method
    public float giveMeAFloat()
    {
        return 123.123f;
    }
    
    // pretend its abstract, we don't provide implementation so use a semi colon not braces!
    public native String doSomeNativeCodingAndReturnAString();
}

```

As with classes, the final modifier does the same thing, it states that the particular method can’t ever be overridden in a subclass, simple as that.

The same applies to the abstract modifier, abstract methods are declared with a signature, a return type, an optional throws clause, but are not actually implemented. The first concrete subclass to extend from the class has to override that method and provide its implementation.

Consider the following example :

```java
package modifiers;

// Don't forget, that since we have one abstract method, the whole class must be marked abstract!
public abstract class OtherMemberModifiers
{
    // I'll get inherited, since I'm public, but I can't be overridden.
    public final String giveMeAString()
    {
        return "hello";
    }

    // Remember, semi colon, no braces!!
    public abstract String giveMeAnotherString();
}

class ClassThatExtends extends OtherMemberModifiers
{
    // I'm the overridden abstract method above!
    @Override
    public String giveMeAnotherString()
    {
        return "another string";
    }
}
```

Remember, abstract methods can’t be private or final, otherwise how would it ever be overridden? Think about it!

Lets cover the synchronized modifiers. Synchronized means that only one thread of execution can pass through that method at a given time, and you can use the modifier just like any other, they can also be marked as final, to prevent subclasses from overriding your implementation. Lets have a look :

```java
package modifiers;


public class SyncModifiers
{
    // Just a synchronized method ;)
    public synchronized String giveMeAString()
    {
        return "hello";
    }

    // I can't be overridden by a subclass
    public final synchronized String giveMeAnotherString()
    {
        return "hello again!";
    }
}
```

The synchronized modifier can also be used on code blocks, but we’ll cover that more in the threading posts.

Well thats it! I hope I’ve explained things well, if you find any mistakes or want to suggest improvements, please let me know!

Happy coding!