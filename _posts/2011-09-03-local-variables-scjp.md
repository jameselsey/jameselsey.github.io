---
title: 'Local Variables, as far as the SCJP is concerned'
date: '2011-09-03T13:32:26+10:00'
permalink: /local-variables-scjp
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
Local variables are variables that are declared locally, funny that eh? This one should be nice and easy, lets have a look at a quick example :

```java
package scope.localvariables;

public class LocalVariables
{
    private String instanceString;

    public void doSomething()
    {
        // local variable called 'localS', initialised
        String localS = "hello";
        // Another initialised local variable, with the final modifier so it can't be changed once initialised
        final String localS2 = "hello again";

        localS = "I've changed, since I'm not final";

        System.out.println(localS);
        System.out.println(localS2);
    }

    public void doSomethingElse()
    {
        String localS;
        // Compiler won't like this, since you haven't initialised 's' with anything
        System.out.println(localS);
    }
}
```

Some really easy things to remember, local variables don’t ever get initialised automatically, so you have to do that yourself. The method above, doSomethingElse, the variable gets declared, but never assigned a value. The compiler won’t assign a default value like it does for an instance varible, so the compiler will complain on the line which tries to print the value to system out. So whatever you do, if you declare a local variable but don’t assign it a value, make sure that you DO assign it a value before you try to use it.

Since variables are declared within a method, they can only be accessed, and actually only live for the duration that method is executing. So if you declare a variable inside a method, but try to access it elsewhere, your code will fail to compile since its scope is only available to the method the declaration resides in. Careful of this one, there are a lot of questions in the exam which will try to catch you out with this!

Local variables can take the final modifier if required, so that once assigned a value it can’t be changed.

Also note, that local variables live on the stack, and not the heap.

Lastly, it is possible to have local variables with the same names as instance variables, this is known as shadowing. You’ve probably seen this countless times before without actually realising, since its quite popular with constructors (with may also have local variables)

```java
package scope.localvariables;

public class MyClass
{
    private String name;
    private int age;

    // Overridden constructor, with 2 local variables that shadow instance variables
    public MyClass(String name, int age)
    {
        this.name = name;
        this.age = age;
    }

    public static void main(String[] args)
    {
        MyClass myClass = new MyClass("James", 25);
        System.out.println("Name is : " + myClass.name + " Age is : " + myClass.age);
    }
}
```

Thats it, hope you’ve enjoyed the show folks, please leave comments or improvement suggestions!

Thanks!