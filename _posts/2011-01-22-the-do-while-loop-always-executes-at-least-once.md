---
title: 'The do-while loop, always executes at least once&#8230;'
date: '2011-01-22T14:32:09+10:00'
permalink: /the-do-while-loop-always-executes-at-least-once
author: 'James Elsey'
category:
    - Java
tag:
    - do
    - java
    - loop
    - OCJP
    - OCPJP
    - scjp

---
As I’ve covered in a previous [post regarding the while loop](/using-while-loops-for-when-you-dont-know-how-long-the-piece-of-string-is), the *do-while*, often refered to as the *do loop* is very similar. Lets have a look at the psuedo code.

```java
do
{
    //do stuff in here
}
while (boolean_expression);
```

So as we can see, the actual body of the loop gets executed at least once before the boolean expression is ever evaluated. Now lets have a look at a real example :

```java
public class MonkeySniffer
{
    public static void main(String[] args)
    {
        do
        {
            System.out.println("Inside the loop!");
        }
        while (3 > 4);
    }
}
```

What the above does, is to print out *Inside the loop!* once, then it checks the boolean expression in the while statement, if it is true then the loop will be repeated, if it is false, in this case it will be since 3 is not greater than 4, then the loop is done and the code will continue.

Thats it really, nothing too fancy here, this loop is really suited to situations where you must do something at least once, but possibly multiple times.

Anything I’ve missed please let me know!

Happy coding.