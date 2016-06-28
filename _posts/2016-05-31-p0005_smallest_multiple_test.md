---
layout: post
title: "Problem 5 - Smallest multiple"
date: 2016-05-31 08:40:13 +0100
categories: [python,euler]
---

Problem 4 gave us an interesting performance issue to chew on. Problem 5 does not
give us that to worry about, and is altogether a simpler affair.

> 2520 is the smallest number that can be divided by each of the numbers from
> 1 to 10 without any remainder.
> 
>What is the smallest positive number that is evenly divisible by all of the
> numbers from 1 to 20?

The simplest, and wrong, solution is to just calculate 20!. This is the product
of all the numbers from 1 to 20, but it's not the lowest that divides by each
of them.

The brute force solution may be to start at 20 and increment, checking for each new
value whether it divides evenly by all numbers 1 to 20. An alternative may involve
using prime factors to save having to divide by numbers that are a product of
primes (e.g. 20 is the product of 2, 2 and 5).

But in the end I realised that there's a simpler way. It's a little brute force,
but involves brute forcing the multiplier rather than the predicate.

The algorithm looks something like this:

    {% highlight python %}
    total = 1

    for n in 2 to 20
        for m in 1 to n
            if total * m divides by n
                total = total * m
                break
    {% endhighlight %}

That is to say, starting with a total of 1, loop through the numbers 2 to 20 and
multiply the total by the lowest value that results in a total that divides by the
number being tested. Given that a number multiplied by an integer will only
ever gain, and never lose, divisors it's a good way to ensure divisibility with
each number 1 to 20 and also each number before it.

**Aside**

I've had to type something like `x % y == 0` to test divisibility a few times. It
is getting dull. So I've added a function. `euler.math.divides_by` takes two ints
and returns `True` if the first can be evenly divided by the second.

    {% highlight python %}
    >>> divides_by(3, 2)
    False
    >>> divides_by(4, 2)
    True
    {% endhighlight %}
    
**End Aside**

So to implement the previously mentioned algorithm I use `reduce` to iterate
over the numbers 2 to 20 with a starting value of 1 and a function to do the
multiplication.

    {% highlight python %}
    from functools import reduce
    from euler.math import divides_by


    def smallest_multiple(limit):
        def multiply_by_lowest_int(total, n):
            lowest_multiplier = next(m for m in range(1, n + 1)
                                     if divides_by(total * m, n))
            return total * lowest_multiplier

        return reduce(multiply_by_lowest_int,
                      range(2, limit + 1), 1)
    {% endhighlight %}

This gives the correct answer of *232792560* in *1ms*.
