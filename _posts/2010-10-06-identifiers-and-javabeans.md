---
title: 'Identifiers and JavaBeans'
date: '2010-10-06T12:37:17+10:00'
permalink: /identifiers-and-javabeans
author: 'James Elsey'
category:
    - Java
tag:
    - 'core java'
    - identifiers
    - java
    - 'java beans naming'
    - 'naming conventions'
    - scjp
---
Identifiers are what we use to identify parts of our code, whether this is a class, method or a variable. There are some ground rules on the names we can give to these identifiers, and it is good to understand the ground rules regarding these.

There are 3 levels, or aspects of identifiers, and are summarised as follows

*Legal Identifiers* – Java has keywords which the compiler uses, and also other rules on what can be used as an identifier, so in order to get your code compiling (and hopefully running too) you’ll need to make sure the names you choose for your variables/classes/methods are legal, more on this below

*Suns’ Java Code conventions* – Technically, you only really need to adhere to the legal identifier rules, but Sun has its own conventions on identifiers and it is good practice to follow suit. You should really familiarise yourself with these and try to follow their conventions where at all possible.

*JavaBeans naming standards* – A further set of recommended indentifier rules. Once again you’re not forced to follow these, but it is good practice to do so, and makes your code more clear and readable to other users.

As I mentioned above, you only have to follow the legal identifier naming rules, but it is good practice to follow the conventions in place as it will make your code a lot neater and easier to read for others. Think of it like this, if you need some help with your coding, and you post some samples to a forum, you’ll get much better responses if people can understand what your code is doing.

### Legal Identifiers

OK, so legal identifiers, your variable names will either be legal (this is good!) or illegal (Bad, very bad, compiler will throw its toys out of the pram). In order to be a legal identifier, you must follow the following rules

1. First letter must be a letter (a, b, c etc), or a currency character ($ for example), or one of the connecting characters such as the underscore (\_)
2. First letter must NOT be a number
3. After the first character, things get more relaxed, and you are able to use any combinations of letters, numbers, currency characters or connecting strings (underscores etc)
4. You can’t use a Java keyword as an indentifier, this would make it illegal
5. Identifiers are case sensitive, that means that the variable myVariable is different to MYVARIABLE

Here are some perfectly legal identifiers

- String _s;
- String $s;
- String ______s;
- String _$;
- String this_isAnIncredibly_long_but_perfectly_valid_identifier;

And here are some illegal identifiers, using these will throw an error so memorise the rules and steer clear!

- String ::;
- String s#
- String .myString;
- String 1s;
- String -s;

### Suns’ Java Code Conventions

Wouldn’t it be great if every Java developer was writing code that followed a set of rules for naming conventions? That would be ideal wouldn’t it? Well in the real world its not going to just happen, but Sun have taken a good step toward achieving this by releasing the Java Code Conventions. This is a short, brief set of guidelines that Sun has produced to help steer developers onto a best practice track of naming our identifiers. It recommends some of the following approaches

**Classes**  
For naming your classes, Sun recommend that you start off with a capital letter, and if you have multiple words then you concatenate them making the first letter of each word a capital, a term often refered to as CamelCase. Classes should also typically be nouns, being names of things, the following are good examples

- Person
- Student
- TextBook

**Interfaces**  
Interfaces should follow the same rules as classes, except the name should typically be an adjective instead of a noun, this helps a coder to indentify them as an interface or a contract easier. A few good examples are

- Runnable
- Drawable
- Serializable
- Throwable

**Methods**  
The first letter of the method name should be lower case, then the usual camelCase rules should apply, some examples are as follows

- getColor
- isAvailable
- doSomething

**Variables**  
Same as with methods, the first letter should be lower case, then camelCase. Some examples are as follows

- myString
- myCar
- player
- studentReferenceNumber

**Constants**  
Contants should be named in upper case, using the underscore as the connecting character between words. Constants are created by declaring the variable as static and final. Some examples are as follows

- HOST_NAME
- PORT_NUMBER
- MAX_WIDTH

### JavaBeans Naming Standards

When you become more involved in your Java development, you will most likely come across the notion of JavaBeans, these are like objects which have instance properties. Typically these properties will be set as private, and you will be using accessor methods to obtain or set them.

Imagine that you have a class called Person, which may have properties such as name and age. The indentifiers I’ve already mentioned just now follow the Sun code convention, however you may wish to make them private, meaning you will most likely need to provide accessor methods, which would follow the Sun conventions, such as

getName  
setAge

Ideally, for setting a variable you should create a method prefixed with set, and for getting a variable you should prefix with get.  
With booleans, is it good practice to prefix with is, such as isAvailable

### Summary

So there you have it, I’ve hopefully explained the basics that you need to know for identifiers. Just remember that your identifiers must at least be legal, but it is also good practice to follow the Sun and JavaBeans naming conventions where at all possible. Please see the following links for further reading. Any comments/suggestions greatly appreciated!

- [JavaBeans Wiki](http://en.wikipedia.org/wiki/JavaBean)
- [Sun naming conventions](http://www.oracle.com/technetwork/java/codeconv-138413.html)