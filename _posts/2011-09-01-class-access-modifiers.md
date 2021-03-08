---
title: 'Class Access Modifiers'
date: '2011-09-01T19:04:41+10:00'
permalink: /class-access-modifiers
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
Class access modifiers define who can see the class, you use it on a daily basis, have a look at the following :

```java
public class ClassAccessModifierExample
{
    public static void main(String[] args)
    {
        // do something!
    }
}
```

There you go, you said “public class”, thats you saying that this class is public, and anything can access it.

It is important to note, that even though a class may be declared as public, you can still have private members within that class. The class access modifier merely states who can gain access to the class.

There are 3 class access modifiers, in other words there are 3 keywords that you can use :

1. **public** – any class can import and use your class.
2. **protected** – any class in the same package as yours can import and use your class, additionally, any class that resides in another package, can extend and use your class. This is only really useful for inner classes.
3. **private** – no other top level class can import and use your class. This is a bit of an odd situation but is useful for inner classes, detailed in another post.

Those are your 3 keywords, however there is actually a 4 level of access, called default. The default access level doesn’t use a keywork, so it would be used like follows :

```java
class ClassAccessModifierExample
{
    public static void main(String[] args)
    {
        // do something!
    }
}
```

So our 4th access level is :

- (**default**) – also called “package-private”, this class is only visible to classes in the same package.

So what is all the fuss about visibility about? Well it lets classes do various things,such as :

1. Create an instance of the class
2. Extend, sublass that class
3. Access members of that class

One last thing to remember, is that a source file can only ever have one public or default class, it can’t have more than one of those. Also, private and protected can’t be at the top level.

- You can’t have a private top level class, its useless.
- You can’t have a protected top level class, since it goes against the principle of the protected access.

Consider the following valid example

```java
// I'm public, and my name matches that of the source file
public class ClassAccessModifierExample
{
    public static void main(String[] args)
    {
        // do something!
    }


    // You can have as many protected classes as you want, accessible
    // to any classes that extend from the top level class, ClassAccessModifierExample
    protected class MyProtectedClass
    {

    }

    // Have as many private classes as you want, they are only accessible to the top level class,
    // ClassAccessModifierExample
    private class InnerClassThatIsPrivate
    {

    }
    
    // Nothing wrong with having a public inner either!
    public class PublicInner
    {
        
    }
}
// Default class here
class TopLevelDefaultClass
{

}
```

Thats it! If you’ve got any suggestions on how to improve this, please leave a comment :)