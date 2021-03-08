---
title: 'Static Imports, how they could and should be used'
date: '2011-01-15T00:38:15+10:00'
permalink: /static-imports-how-they-could-and-should-be-used
author: 'James Elsey'
category:
    - Java
tag:
    - import
    - java
    - OCJP
    - OCPJP
    - scjp
    - static

---
One of the new features of Java 5 is the [static import](http://download.oracle.com/javase/1.5.0/docs/guide/language/static-import.html), its quite simple in nature. You have probably done something like the following many times before :

```java
public class Runner
{
    public static void main(String[] args) 
    {
        System.out.println("hello");
        Math.max(1,2);                
    }
}
```

Nothing too complex, you’re refering to static methods on the [System](http://download.oracle.com/javase/1.5.0/docs/api/java/lang/System.html)and [Math](http://download.oracle.com/javase/6/docs/api/java/math/package-summary.html)classes. If you use a lot of static methods from other classes, you might decide that typing the class name is wasted effort. In this case we can use a static import.

The static import allows you to import the Math and System classes, and then you can call their static methods without having to reference the class name, such as :

```java
import static java.lang.Math.*;
import static java.lang.System.*;

public class Runner
{
    public static void main(String[] args)
    {
        out.println("hello");
        max(1,2);
    }
}
```

So as you can see, since we’ve imported the class as a static import, we no longer need to reference the class name before calling a static member.

Of course, there are some drawbacks from taking this approach. Say for example you are performing static imports from multiple classes, that coincidently happen to have methods with the same signature (names and input parameters), how does the class behave in such situation? Consider the following :

```java
package com.test.static1;

public class StaticClassOne 
{
    public static String hello()
    {
        return "hello";
    }
}

package com.test.static2;

public class StaticClassTwo
{
    public static String hello()
    {
        return "hello";
    }
}

package com.test;

import static com.test.static1.StaticClassOne.*;
import static com.test.static2.StaticClassTwo.*;
import static java.lang.System.*;

public class Runner
{
    public static void main(String[] args) 
    {
        out.println(hello());               
    }
}
```

From this example, we are performing 2 static imports, the static method which we are referencing appears in both imports, so the compiler is unable to know which one it should be using, you would get the following compiler error

```
reference to hello is ambiguous, both method hello() in 
com.test.static2.StaticClassTwo and method hello() 
in com.test.static1.StaticClassOne match
```

This does leave room for error, and no developer is perfect. This makes me side slightly towards not using static imports unless absolutely necessary, or in situations where I can be sure there won’t be a conflict. Personally, I actually find it easier to prefix static methods with their class names, so I can immediately see which class the static member belongs to.