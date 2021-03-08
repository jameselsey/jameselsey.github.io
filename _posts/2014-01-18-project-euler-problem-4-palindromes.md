---
title: 'Project Euler, problem 4, palindromes'
date: '2014-01-18T21:06:00+10:00'
permalink: /project-euler-problem-4-palindromes
author: 'James Elsey'
category:
    - Mathematics
tag:
    - maths
    - project-euler
---
[Problem 4](http://projecteuler.net/problem=4):

> A palindromic number reads the same both ways. The largest palindrome made from the product of two 2-digit numbers is 9009 = 91 × 99.
> 
> Find the largest palindrome made from the product of two 3-digit numbers.

Firstly I decided to try and solve the problem using two 2-digit numbers so I can understand how the creator of this problem got to 9009.

In about 15 minutes I was able to come up with this brute force hack:

```java
public class Problem4 {

    public static void main(String[] args) {
        solve();
    }

    private static void solve() {
        int highestPalindrome = 0;

        for (int left = 99; left &gt; 2; left--) {
            for (int right = 99; right &gt; 2; right--) {
                int candidate = left * right;
                if (isPalindrome(candidate)) {
                    System.out.println(format(&quot;Palindrome found! Using %d * %d = %d &quot;, left, right, candidate));
                    if (candidate &gt; highestPalindrome) {
                        highestPalindrome = candidate;
                    }
                }
            }
        }
        System.out.println(&quot;Highest palindrome is &quot; + highestPalindrome);
    }

    private static boolean isPalindrome(int palindrome) {
        String palindromeString = &quot;&quot; + palindrome;
        String reversed = new StringBuilder(palindromeString).reverse().toString();
        return palindromeString.equals(reversed);
    }
}
```

Which gives the output of

```
Highest palindrome is 9009
```

Excellent, so we’re on the right track. The next thing I did was alter the loop statements to start at 999, which then prints the following

```
Highest palindrome is 906609
```

Which is the correct answer!

Any suggestions on improvements? Please comment!