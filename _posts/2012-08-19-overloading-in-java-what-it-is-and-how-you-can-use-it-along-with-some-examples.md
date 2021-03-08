---
title: 'Overloading in Java, what it is and how you can use it, along with some examples'
date: '2012-08-19T19:57:36+10:00'
permalink: /overloading-in-java-what-it-is-and-how-you-can-use-it-along-with-some-examples
author: 'James Elsey'
category:
    - Java
tag:
    - core-java
    - java
    - OCPJP
    - overloading
    - overriding
    - scjp
---
There are times when developing, when you need to do similar behaviour but with differing input arguments, for example you may have a method that prints something to the console. You may need to print Strings, Integers, and all sorts of other objects. You could (if you really wanted), create a method for each one with some kind off suffix, such as printString(), printInteger(), printMyObject(), however there is a much neater way of doing this, called overloading.

Overloading basically allows you to reuse the method name multiple times, providing you supply a different number and type of input arguments for each one, so that the compiler can tell them apart. This means we can have methods like print(String string), print(Integer integer) and print(String string, Integer integer), this makes code structure a lot neater and is generally considered good practice.

This is quite different to overriding, and you need to be careful not to get the two mixed up, you may be interested in a [previous post](/overriding-and-overloading-a-n00bs-explanation) where I compared the two.

This is one example of overloading that I have up on my [SCJP examples Github](https://github.com/jameselsey/SCJP-OCPJP-SampleCode) project :

```java
package com.jameselsey.demo.scjp.oo_concepts;

/**
 * Author:  JElsey
 * Date:    17/08/2012
 *
 * A simple demonstration of what Overloading is and how it can be used. Overloading allows you to have a method with the same
 * name, but overloaded with differing arguments, this is applicable to methods and constructors
 */
public class Overloading
{
    public static void main(String[] args)
    {
        Overloading ol = new Overloading();

        // Same method name, but overloading with differing input arguments
        ol.sayAboutAPerson();
        ol.sayAboutAPerson("James");
        ol.sayAboutAPerson("James", 26);

        // now lets instantiate an object using overloaded constructors
        Person p1 = new Person();
        Person p2 = new Person("James");
        Person p3 = new Person("James", 26);
        System.out.println(p1);
        System.out.println(p2);
        System.out.println(p3);
    }

    public void sayAboutAPerson()
    {
        System.out.println("We don't know anything about this person");
    }

    public void sayAboutAPerson(String name)
    {
        System.out.println("Persons name is " + name);
    }

    public void sayAboutAPerson(String name, int age)
    {
        System.out.println("Persons name is " + name + " and age is " + age);
    }
}

class Person
{
    String name;
    int age;

    // Look! We can overload constructors too, particulary helpful if you want the flexibility to
    // instantiate objects using a variety of arguments
    public Person()
    {

    }

    public Person(String name)
    {
        this.name = name;
    }

    public Person(String name, int age)
    {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString()
    {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```