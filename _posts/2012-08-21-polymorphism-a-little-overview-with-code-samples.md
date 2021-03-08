---
title: 'Polymorphism, a little overview with code samples'
date: '2012-08-21T19:58:15+10:00'
permalink: /polymorphism-a-little-overview-with-code-samples
author: 'James Elsey'
category:
    - Java
tag:
    - OCPJP
    - polymorphism
    - scjp
---
Polymorphism basically means that one thing can take many forms, and this is particularly useful for programmers, as it allows us to treat similar types of object in a single manner, yet the objects may actually be slightly different at runtime.

One application of this might be where we have an animal class, we know that the animal class has methods like makeNoise(), move() and eat(), and we can happily develop our application against this. However, we may then like to extend the animal class, and create subclasses like Dog, Fish, and Cat, each which would inherit those methods, and optionally override the implementation to do things that are a little more Dog specific.

Consider the following example that I have hosted up on my [SCJP samples](https://github.com/jameselsey/SCJP-OCPJP-SampleCode) Github page:

```java
package com.jameselsey.demo.scjp.oo_concepts;

/**
 * Author:  JElsey
 * Date:    16/08/2012
 *
 * A few examples of polymorphism, meaning that one thing can represent many, or in another words, "morphed" in many (poly) ways
 */
public class Polymorphism
{
    public static void main(String[] args)
    {
        Animal animal;

        //  Polymorphic assignments here, animal being assigned as one of its sub-classes
        animal = new Cat();
        System.out.println("Cat makes a noise : " + animal.makeANoise());
        animal = new Dog();
        System.out.println("Dog makes a noise : " + animal.makeANoise());
        animal = new Fish();
        System.out.println("Fish makes a noise : " + animal.makeANoise());

        // Polymorphic array, declared as type Animal but contains sub-types
        Animal[] theAnimals = new Animal[]{new Cat(), new Dog(), new Fish()};
        for (Animal a : theAnimals)
        {
            // Since they're all of type animal, they all have a makeANoise() method, but the actual
            // implementation varies
            System.out.println(a.makeANoise());
        }
    }
}

abstract class Animal
{
     public abstract String makeANoise();
}

class Cat extends Animal
{
    @Override
    public String makeANoise()
    {
        return "Meow";
    }
}

class Dog extends Animal
{
    @Override
    public String makeANoise()
    {
        return "Woof";
    }
}

class Fish extends Animal
{
    @Override
    public String makeANoise()
    {
        return "Gurgle";
    }
}
```

Most of the time, you’ll actually use polymorphism without even realising, you just do it naturally. You’ll often find that it is useful especially when you “code to the interface, not the implementation”.

That is all.