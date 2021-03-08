---
title: 'Overriding and Overloading, a n00bs explanation'
date: '2011-01-15T00:55:25+10:00'
permalink: /overriding-and-overloading-a-n00bs-explanation
author: 'James Elsey'
category:
    - Java
tag:
    - java
    - OCJP
    - OCPJP
    - overloading
    - overriding
    - polymorphism
    - scjp
---
This is easily one of the most confusing concepts to a newbie, but to be honest its relatively simple. The most confusing part is that both start with the letter ‘O’. You’ll really need to understand this if you plan to get anywhere as a programmer, as you can bet you’ll be asked “what is the difference between overriding and overloading” during a technical interview.

Heres a brief heads up on what each one means, to get us started :

- **Overriding** – Your a subclass, you inherit some of your parent classes methods, but you don’t quite like them, so you override them with your own implementation. (Or, you are a concrete class extending an abstract class, you’re forced to implement the abstract methods so this is technically overriding them. More on this later)
- **Overloading** – You have several methods that have the same method name and return parameter, but take different input parameters.

*So what is the difference between the two?*  
Overriding is when a class provides its own implementation of a method which is inherited from its superclass, or when a concrete class implements abstract methods from its abstract superclass. Overloading is when two or more methods have the same name, but different input parameters.

Each deserves their own explanation, so I’ll tackle these in separate posts.