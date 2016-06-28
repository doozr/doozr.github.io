---
layout: post
title: "Problem 1 - Multiples of 3 and 5"
date: 2016-05-27 11:35:51 +0100
categories: [python,euler]
---

As suspected, the first few questions were not taxing. The first is a fairly
basic variation on the fizzbuzz problem, but without the complexity of strings.

> If we list all the natural numbers below 10 that are multiples of 3 or 5, we get 3, 5, 6 and 9.
> The sum of these multiples is 23.
> 
> Find the sum of all the multiples of 3 or 5 below 1000.

I have decided to try and make my solutions into generalised functions that
accept parameters rather than just solving the single given case. I'm not
sure if it will be possible in all cases, or if there will be issues with
the shear scale of some of the problems or, in odd situations, limits with
the chosen algorithms.

A solution to the given problem generalised only to accept a variable upper
bound (to test with 10 and 1000) is:

    {% highlight python %}
    def multiples_of_three_and_five(limit):
        return sum(x for x in range(1, limit) if not x % 3 or not x % 5)
    {% endhighlight %}

Generalised further it would be better to have no upper bound
at all and to generate an infinite list of values divisible by some arbitrary
list of numbers. The user could then take from the sequence until some
criteria is met and do something with result.

    {% highlight python %}
    from itertools import count, takewhile


    def multiples_of(ns):
        return (x for x in count(1) if any(x % n == 0 for n in ns))


    def multiples_of_three_and_five(limit):
        return sum(takewhile(lambda x: x < limit, multiples_of([3, 5])))
    {% endhighlight %}

I am handling each of the problems as a unit test that asserts the answer, once
I have done enough investigation to be sure of what the answer is. This will
give me a way to quickly check that optimisations and refactoring have worked
as intended. It is my intention to keep each problem's runtime to under a second,
although within a handful of milliseconds would be nicer.

    {% highlight python %}
    def test_0001_multiples_of_three_and_five():
        assert multiples_of_three_and_five(1000) == 233168
    {% endhighlight %}

There we have it. The answer is *233168*. Runtime of the test is around *3ms*.
