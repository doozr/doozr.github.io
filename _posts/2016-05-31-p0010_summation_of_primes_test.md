---
layout: post
title: "Problem 10 - Summation of primes"
date: 2016-05-31 13:12:37 +0100
categories: [python,euler]
---

Back on the primes for problem 10. Could be an interesting speed issue.

> The sum of the primes below 10 is 2 + 3 + 5 + 7 = 17.
>
> Find the sum of all the primes below two million.

Sounds simple enough. Use the sieve from problem 3 to generate all the primes
and then add them up.

    {% highlight python %}
    from euler.prime import sieve


    def summation_of_primes(n):
        return sum(sieve_of_eratosthenes(n))
    {% endhighlight %}

It worked! Except it took over a second. A faster prime algorithm is needed.

*time passes...*

After some significant research, I have a faster prime algorithm. It is known
as the RWH algorithm although I don't know why. I've written a Python 3
implementation.

    {% highlight python %}
    def sieve(n):
        marked = [True] * (n//2)
        for i in (x for x in range(3, int(n**0.5 + 1), 2) if marked[x // 2]):
            num_to_update = ((n - i * i - 1) // (2 * i) + 1)
            marked[i * i // 2::i] = [False] * num_to_update
        return chain([2], (2 * i + 1
                           for i in range(1, n // 2)
                           if marked[i]))
    {% endhighlight %}

Not the most readable of things, I know.

The strangest bit is in the middle where a subset of a list is assigned to a
number of values. This is apparently a thing Python can do. For example:

    {% highlight python %}
    >>> a = [1,2,3,4,5,6,7,8,9,0]
    >>> a[2::3]
    [3, 6, 9]
    >>> a[2::3] = ['A', 'B', 'C']
    >>> a
    [1, 2, 'A', 4, 5, 'B', 6, 7, 'C', 0]
    {% endhighlight %}

The precise detail of the calculation is left to the reader, but basically the
extended slice assignment has two parts; the bit being replaced that starts
at `i**2` and continues to the end in increments of `i`, and the calculation on
the right hand side. This calculation works out how long the slice is without
going to the extent of creating one and calling `len()` on it. This is gets a
3x speed boost.

Thanks to the RWH sieve the speed has increased somewhat. The correct answer
of `142913828922` is reached in a mere `138ms`. Better, but not great. I may
revisit later when I get my head around wheel factorisation.

*Bonus:* Changing the prime generator code has sped problem 7 up from
*46ms* to a tiny *12ms*. Good stuff.
