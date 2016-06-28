---
layout: post
title: "Problem 9 - Special Pythagorean triple"
date: 2016-05-31 12:15:06 +0100
categories: [python,euler]
---

Back to the more straightforward now for problem 9. We have to find a
Pythagorean triple (read; right angled triangle) whose values add up to 1000.

> A Pythagorean triplet is a set of three natural numbers, a < b < c, for which,
> 
> a2 + b2 = c2
> For example, 32 + 42 = 9 + 16 = 25 = 52.
> 
> There exists exactly one Pythagorean triplet for which a + b + c = 1000.
> Find the product abc.

Given that Pythagoras' triangle is represented by:

    a^2 + b^2 = c ^2

And given that:

    a + b + c = 1000

We must figure out how to put these together. There is a simple brute
force method where we check, for each possible value of `a` and `b` where
`a >= b`, and the predicate `a^2 + b^2 = (1000 - a - b)^2` holds.

This solution is expressed in code as:
 
    {% highlight python %}
    def special_triple(num):
        return next((a, b, num - b - a)
                for b in range(1, num)
                for a in range(1, num - b)
                if a**2 + b**2 == (num - b - a)**2)


    def special_triple_product(num):
        a, b, c = special_triple(num)
        return a * b * c
    {% endhighlight %}

This results in the correct answer but takes a whopping *330ms* to run. Way
beyond my adjusted target of 100ms.

The solution to this issue is not to try and limit the search scope or similar
nonsense, but to calculate the values directly. Most Pythagorean triples can be
discovered by choosing two values, m and n, and using this formula:

    (m^2 – n^2)^2 + (2mn)^2 = (m^2 + n^2)^2

Which is to say that:

    a = m^2 - n^2
    b = 2mn
    c = m^2 + n^2

This can be simplified thus:

    2mn + (m^2 - n^2) + (m^2 + n^2) = 1000
    2mn + 2m^2 = 1000
    2m(n + m) = 1000
    
Given that we know that `m > n` we can very quickly work out the values of m
and n through iteration.

    {% highlight python %}
    def mn(num):
        return next((m, n)
                    for m in range(1, num)
                    for n in range(1, m)
                    if m * (m + n) == num // 2)


    def pythagoras_triangle(num):
        m, n = mn(num)
        return (m**2 - n**2,
                2 * m * n,
                m**2 + n**2)


    def special_triple_product(num):
        a, b, c = pythagoras_triangle(num)
        return a * b * c
    {% endhighlight %}


This solution gives the correct answer of *31875000* in *3ms*. Much better.
