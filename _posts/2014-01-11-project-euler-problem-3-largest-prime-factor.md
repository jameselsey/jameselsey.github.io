---
title: 'Project Euler, problem 3, largest prime factor'
date: '2014-01-11T14:41:25+10:00'
permalink: /project-euler-problem-3-largest-prime-factor
author: 'James Elsey'
category:
    - Mathematics
tag:
    - math
    - project-euler
---
[Problem 3](http://projecteuler.net/problem=3) :

> The prime factors of 13195 are 5, 7, 13 and 29.
> 
> What is the largest prime factor of the number 600851475143 ?

To solve this, I needed to understand the process of prime factorisation (surprisingly, it is something which I was never taught at school/uni).

What we need to do, is to take the number 13195, then try and divide it by the smallest prime number; 2.

If it doesn’t divide equally, we try it with the next prime number; 3, and so on, until it divides evenly with no remainder.

So 13195 divides into 5 evenly, and we get a result of 2639.

We can retain that number 5, but since we’re only interested in the largest prime factor, we reset our divisor count to 2 and start the process again, trying to divide 2639 until we find a number that it divides evenly by, which happens to be 7.

- 13195 / 5 = 2639
- 2639 / 7 = 377
- 377 / 13 = 29

On the last stage of prime factorisation, we’re trying to find a number that fits into 29 evenly, but there isn’t one, so we’re left with the prime number of 29.

We’re left with the prime factors of 5, 7, 13 and 29.

I found that the [MathsIsFun.com ](http://www.mathsisfun.com/prime-factorization.html)had a good article on prime factorisation, with several examples and explanations.

Here is my solution using Java

```java
/**
 * The prime factors of 13195 are 5, 7, 13 and 29.
 * <p/>
 * What is the largest prime factor of the number 600851475143 ?
 */
public class Problem3 {

    public static void main(String[] args) {
        System.out.println(solve(600851475143l));
    }

    public static int solve(long number) {
        int i;

        // start dividing by 2, as that is the smallest prime number, keep going until we're trying to divide the number by itself,
        // this indicates we've finished.
        for (i = 2; i <= number; i++) {
            //We're only interested in "i" if it divides evenly by the number
            if (number % i == 0) {
                // divide number by i and re-assign, so we can start counting up from 2 again
                number /= i;
                // the for loop will increment i, however if number is divisible by i with no remainder, we want to try again.
                // for example, if we divide number 2000 by 2, we want to decrement i so we can try to divide the resulting 1000 by 2 again
                i--;
            }
        }

        return i;
    }
}
```

Don’t forget the “L” on the end of the number, we need to use a long as its too large to fit in an int.

The answer is 6857