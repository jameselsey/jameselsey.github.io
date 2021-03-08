---
title: 'Declaration Rules in Java'
date: '2011-01-03T13:52:50+10:00'
permalink: /declaration-rules-in-java
author: 'James Elsey'
category:
    - Java
tag:
    - 'declaration rules'
    - java
    - OCJP
    - OCPJP
    - scjp

---
Another one of my short and sweet posts in my [SCJP ](/scjp-ocjp-exam-objectives-and-what-you-need-to-study-for-each-section)study guide section. This one is quite straightforward, so we’ll keep it quick, since I know you’d much rather be facebooking or watching cats do strange things on youtube…

Declaration rules, in regards to the class files themselves, meaning what you can and can’t do. Consider the following simple example:

```java
public class MonkeySniffer
{
   public static void main(String[] args)
   {
      System.out.printf("I smell a Gibbon!");
   }
}

```

Thats a simple class that will just print out some text. Notice that I didn’t include any package or import statements, as they are completely optional. Also note that the java.lang.\* package is imported automatically, so you don’t need to manually import String for example.

Here are some other things that you must take into consideration when declaring your source files.

1. If you have a package statement, it must be the first “code” line in the source file (you can have comments above it though).
2. If you have any import statements, they must come after the package statement (or at the start of the file if there is no package statement) and must come before the class declaration.
3. Package and import statements will *apply to all classes* in the source file, whether you like it or not.
4. A source file can only have **one** package statement, but you can have as many imports as you require.
5. A source code can only have **one** public class.
6. A file can have more than one non-public class; for example you can declare private inner classes, this might be useful if you have a POJO that is exclusively used in your class.
7. If the source file contains a public class, the name of the source file **must** match (in my example above, since I have a public class called MonkeySniffer, the source file must be named MonkeySniffer.java).
8. If your source file doesn’t have a public class, i.e., it has only private classes, then there are no naming restrictions on the source file.

Have a look at the following example, it covers some of the above points.

```java
// You can make comments before the package statement if you wish
package com.jameselsey.zoo;

/*
    Import statements (if there are any) must come after the package
    and before the class declaration.
 */
import java.util.ArrayList;
import java.util.List;

public class MonkeySniffer
{
    public static void main(String[] args)
    {
        List<String> myList = new ArrayList<String>();
        myList.add("Gibbon");
        System.out.println("I smell a " + myList.get(0));

        MonkeyInners monkeyInners = new MonkeyInners();
        System.out.println(monkeyInners.inners);
    }
}

class MonkeyInners
{
    String inners = "This is an inner!";
}

```

So there you have it, brielfy touched on the declaration rules (regarding classes). Next stop should be Class Access Modifiers, check out my [SCJP/OCJP](/scjp-ocjp-exam-objectives-and-what-you-need-to-study-for-each-section) page for more details.

As always, comments greatly welcomed!

Happy coding :)