---
title: 'String concatenation operator'
date: '2011-09-07T19:06:57+10:00'
permalink: /string-concatenation-operator
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
The string concatenation operator is a bit like a hedgehog, it looks cute and sweet, but try to grab hold of it quickly and you’ll soon know about it…

![Hedgehog](/assets/post_images/2011/hedgehog.jpg)

It’s actually quite simple, however there are some traps that are very easily overlooked (just got caught out with it on a mock test, so I’m here to rant).

The rule of thumb is, that if either one of the operands being “+” is a String, then concatenation will occur, but if they are both numbers, arithmetic addition will occur. Sounds simple right? What do you think the following equate to?

```java
package operators;
public class MockTest
{
    public static void main(String[] args)
    {
        System.out.println(3 + 2);
        System.out.println(3 + "s");
        System.out.println("s" + 2);
        System.out.println("hello " + "world");
    }
}
```

Think you know it? Well its going to be : 5, 3s, s2. The first one is an arithmetic addition, since both sides of the operator are numeric. The second and third are String concatenation, because at least one of them is a String. The fourth one are both Strings, so concatenation once again.

those are the simple ones, however it can get a lot more tricky, have a look at these common ones that those exam creators try to catch you with!

```java
package operators;

public class MockTest
{
    public static void main(String[] args)
    {
        // Inside brackets are evaluated first, so it'll be 7String
        System.out.println( (3 + 4) + "String");

        /* This is the little bugger that caught me, I vouched for 34String, but its actually 7String.
         Concatenation happens from left to right, one at a time, so it'll break it down to:
            3 + 4 then...
            <thing on left> + "String"

         Careful on these ones!
        */
        System.out.println(3 + 4 + "String");

    }
}


```

So remember, it’ll evaluate from left to right, one “+” operator at a time. Brackets are always evaluated first however.

Take my examples and have a play!

Happy coding (or raging at the screen if the mock tests catch you out…)