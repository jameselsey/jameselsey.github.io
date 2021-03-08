---
title: 'Static members'
date: '2011-09-03T14:06:41+10:00'
permalink: /static-members
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
Statics are a rather strange beast, they belong to no instance of a class, they have no fixed abode, other than their class..

Consider the following example :

```java
package declarations;

public class StaticMembers
{
    private static String message = "hello";
    private String instanceMessage = "hello from the instance!";
    
    
    public static void main(String[] args)
    {
        // You "can" create a new instances and call the static member, but its a bit redundant....
        StaticMembers staticMembers = new StaticMembers();
        System.out.println(staticMembers.message);

        // ..Since you can just call it directly, as statics don't belong to any instances
        System.out.println(StaticMembers.message);
        
        // You can't do this, since how would the static know which "copy" of the instance method to get?
        System.out.println(instanceMessage);
        
        // ..But you can do this, since you have an instance..
        System.out.println(staticMembers.instanceMessage);
        
    }
}

```

Statics don’t belong to any instance, they belong to the class itself. So there is only ever one copy of a static member (method or variable). Statics can’t ever access an instance variable directly, why not? Think about it, if a static wants to obtain an instance member variable, there may be hundreds, how would it know which one to get? You can however, declare and initialise an instance member from a static, and then access its members, as we’ve done in the example above.

Take my example, and have a play with it, statics are actually quite straightforward once you understand how they behave, but be careful, the exam will try and trick you out with statics accessing instance members directly!

Happy coding!