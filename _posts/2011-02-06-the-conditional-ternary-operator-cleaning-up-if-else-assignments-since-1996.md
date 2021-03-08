---
title: 'The Conditional (ternary) operator, cleaning up if-else assignments since 1996'
date: '2011-02-06T15:45:41+10:00'
permalink: /the-conditional-ternary-operator-cleaning-up-if-else-assignments-since-1996
author: 'James Elsey'
category:
    - Java
tag:
    - conditional
    - java
    - OCJP
    - OCPJP
    - operator
    - scjp
    - ternary

---
The conditional operator, or ternary operator as it is otherwise known is a great way for assigning variables based on boolean tests. For example you may have seen the following :

```java
public class MonkeySniffer
{
    public static void main(String[] args)
    {

        int x = 5;
        String s;

        if (x < 10)
        {
            s = "Its less than 10";
        }
        else
        {
            s = "its greater than 10";
        }
        System.out.println(s);
    }
}
```

Pretty simple right? However that if-else statement is a bit messy considering all we’re doing is checking a condition and then using the output of that to assign a literal to our String reference.

A ternary operator allows us to perform the conditional check AND the assignment all in one line, it looks a bit like this in its psuedo code format

```
condition ? value if true : value if false 
```

Lets modify the above sample to use the ternary operator. In plain English, we say that s is going to be assigned from either the true or the false condition based on the condition.

```java
public class MonkeySniffer
{
    public static void main(String[] args)
    {

        int x = 5;
        String s;
        s = x < 10 ? "Its less than 10" : "its greater than 10";
        System.out.println(s);
    }
}
```

How much neater is that?! What happens is, the condition is evaluated, i.e., the “x Further Reading :  
[Wikipedia conditional operator](http://en.wikipedia.org/wiki/%3F:)