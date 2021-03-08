---
title: 'The instanceof Operator'
date: '2011-09-07T13:30:22+10:00'
permalink: /the-instanceof-operator
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
The instanceof operator is a great way for checking if a reference variable is of a given type, in other words, does it pass the IS-A inheritance test. Ask yourself, is a Cat a Dog? A Dog a Cat? Or is a Cat an Animal and a Dog an Animal? In true Harry Hill style, there is only one way to decide….FIGHT!!!!

![cat-dog-fight](/assets/post_images/2011/cat-dog-fight.jpg)

Have a look at my following example :

```java
package operators;

public class InstanceOfOperatorDemo
{
    public static String myString = "James";

    public static void main(String[] args)
    {
        // myString variable is of type String, so returns true
        System.out.println("myString instanceof String : " + (myString instanceof String));
        // Even though myString is a String, a String is actually an Object (extends from Object), so the below is also true
        System.out.println("myString instanceof Object : " + (myString instanceof Object));

        B b = new B();
        // B is technically an instance of the implemented interface, so this should return true
        System.out.println("B instance of Foo : " + (b instanceof Foo));
        // This should return true also for the same reasons as above
        System.out.println("A new A() instance of Foo : " + (new A() instanceof Foo));
        // An A isn't an instance of a B, its the other way around; a B IS-A A, so returns false
        System.out.println("A new A() instance of B : " + (new A() instanceof B));

        // Any checks with null always return false, since null isn't an instance of anything
        System.out.println("null instanceof Object : " + (null instanceof Object));

        int[] myInts = new int[10];
        // Even though this array contains primitives, the array itself is still an Object at heart..
        System.out.println("myInts instanceof Object : " + (myInts instanceof Object));
    }
}

interface Foo {}
class A implements Foo {}
class B extends A {}

```

As we can see, a String IS-A Object, so that will pass the instanceof test. It is also important to note, that interfaces are also included in the instanceof check, so by checking if a class or a subclass is an instanceof an interface will return true.

Arrays, no matter what they hold, are always objects themselves, so they can be checked for instanceof too, of which they are Objects, as shown above.

It is important to note, that you can’t use instanceof across class hierarchies, this is something they’ll try to trick you out of in the exam, such as comparing a cat against a dog, you can compare up the hierarchy, but not across it, have a look at this example :

```java
package operators;

public class InstanceOfOperatorCrossHierarchyDemo
{
    public static void main(String[] args)
    {
        // a Cat IS-A Dog, plain and simple
        System.out.println("Is cat an instanceof Animal? : " + (new Cat() instanceof Animal));

        // This will fail to compile, since we are not allowed to instanceof check across the
        // object hierarchy, a Cat is not an instance of Dog, so compiler fails
        System.out.println("Is cat an instanceof Dog? : " + (new Cat() instanceof Dog));
    }
}

class Animal {}
class Dog extends Animal {}
class Cat extends Animal {}

```

That’s it! A nice and easy one, please leave me comments if you can suggest improvements!

Happy coding.