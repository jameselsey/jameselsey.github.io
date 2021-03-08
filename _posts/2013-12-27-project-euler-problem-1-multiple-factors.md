---
title: 'Project Euler, problem 1, multiple factors'
date: '2013-12-27T19:11:15+10:00'
permalink: /project-euler-problem-1-multiple-factors
author: 'James Elsey'
category:
    - Mathematics
tag:
    - maths
    - project-euler
---
> If we list all the natural numbers below 10 that are multiples of 3 or 5, we get 3, 5, 6 and 9. The sum of these multiples is 23.
> 
> Find the sum of all the multiples of 3 or 5 below 1000.

To solve this in a brute force manner, we can count up to 1000, for each number we check to see if it divides into 3 and 5 leaving no remainder. If it does, they we add that number into our running total.

Heres my solution using Java.

```java
public class Problem1 {

    public static void main(String[] args) {
        System.out.println(solve(1000));
    }

    public static int solve(final int subject) {
        int sum = 0;
        for (int i = 0; i < subject; i++) {
            if ((i % 3 == 0) || (i % 5 == 0)) {
                sum += i;
            }
        }
        return sum;
    }
}
```

The answer is 233168