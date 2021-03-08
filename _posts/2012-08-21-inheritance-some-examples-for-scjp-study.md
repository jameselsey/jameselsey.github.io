---
title: 'Inheritance, some examples for SCJP study'
date: '2012-08-21T19:42:30+10:00'
permalink: /inheritance-some-examples-for-scjp-study
author: 'James Elsey'
category:
    - Java
tag:
    - inheritance
    - java
    - OCPJP
    - oop
    - scjp
---
Inheritance is a practice of one thing extending another. So you can more specific subclasses of a class. Inheritance is available in most programming languages, and in Java it is implied using the extends keyword.

For example, you may have several objects like Car, Boat, Truck, these may all have common behaviour and state between them. You could duplicate the shared members between them, but that can lead to a maintenance nightmare for some poor man in the future. The best option would be too look at inheritance, and try to move common, generic code to a superclass, such as Vehicle.

Then, your subclasses can all extend Vehicle, and automatically inherit the shared members, so you only have them coded once. Inheritance can do down as many levels as you like, and both classes and interfaces can use inheritance (a class can extend another class, and an interface can extend another interface, but never implement it).

In Java, you can only extend a single class at a time, which prevents multiple inheritance that you would otherwise get in another language.

Consider the below sample that I have on my [SCJP samples](https://github.com/jameselsey/SCJP-OCPJP-SampleCode) Github project, which portrays a simple inheritance structure:

```java
package com.jameselsey.demo.scjp.oo_concepts;

/**
 * Author:  JElsey
 * Date:    16/08/2012
 *
 * Sample of inheritance, showing a few classes that extend from each other, there is a little bit of polymorphism in here
 * too, can you spot it?
 */
public class Inheritance
{
    public static void main(String[] args)
    {
        Grandfather grandfather = new Grandfather();
        Daddy daddy = new Daddy();
        Son son = new Son();

        // The Grandfather is at the top of the inheritance tree (extends from java.lang.Object), so he only has wisdomLevel
        System.out.println("The grandfather has a wisdom level of " + grandfather.wisdomLevel);

        // The Daddy is strong, and also inherits his wisdom
        System.out.println("The daddy is " + daddy.strengthLevel + " and also gets the grandfathers wisdom: " + daddy.wisdomLevel);

        // The boy is lucky, hes very energetic, but also inherits strength from his dad, and wisdom from his grandfather
        System.out.println("The son is " + son.energyLevel + " and " + son.strengthLevel + " and " + son.wisdomLevel);

        // Inheritance also works on methods too, since they're all part of the same family, we can safely put the
        // family name in the grandfather, both the son and daddy will inherit that method
        System.out.println("Grandfathers surname : " + grandfather.getFamilyName());
        System.out.println("Daddys surname : " + daddy.getFamilyName());
        System.out.println("Sons surname : " + son.getFamilyName());
    }
}

class Grandfather
{
    String wisdomLevel = "Wise";

    public String getFamilyName()
    {
        return "Jones";
    }
}

class Daddy extends Grandfather
{
    String strengthLevel = "Strong";
}

class Son extends Daddy
{
    String energyLevel = "Energetic";
}
```