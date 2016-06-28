---
layout: post
title: "Problem 3 - Prime factors"
date: 2016-05-27 13:34:05 +0100
categories: [python,euler]
---

Problem 3, then, and we're into something a bit more number-crunchy.

> The prime factors of 13195 are 5, 7, 13 and 29.
> 
> What is the largest prime factor of the number 600851475143 ?

What indeed?

For some reason my first instinct for this was to generate a list of every possible
prime up to the square root of 600851475143 and then, in reverse, check each one to
see if it is a factor.

As it turns out, that's a stupid way to do it. But I did it, and ended up with
something quite nifty as a result. To generate the list of all the primes as
quickly as possible I implemented the Sieve Of Eratosthenes. Not the fastest
way to generate primes, but much better than checking every divisor of every
odd number from here to wherever. The only problem is that it must be capped and,
for large numbers of primes, can be memory intensive due to the large cache it
uses.

Here is my sieve of Eratosthenes.

    {% highlight python %}
    def sieve(upper_bound):
        marked = [0] * upper_bound
        yield 2
        for value in (x for x in range(3, upper_bound, 2) if marked[x] == 0):
            yield value
            for i in range(value, upper_bound, value):
                marked[i] = 1
    {% endhighlight %}

This function has proved useful in other problems, and is in `euler.prime`.

The terrible implementation is as follows.

    {% highlight python %}
    from math import sqrt
    from euler.prime import sieve


    def reverse_primes(start):
        return reversed(list(sieve(start)))


    def highest_prime_factor(x):
        limit = int(sqrt(x) + 1)
        return next(y for y in reverse_primes(limit) if not x % y)
    {% endhighlight %}

It calculates the highest prime of 600851475143 in a mere 240ms. Amazing.

Now, technically it falls within the one second goal, and I left it like this
for a while as job done. But it's actually a terrible way to do it and I
figured there must be a better way. Like, err, actually calculating the prime
factors.

I think I just got into the mode of "generate some things and filter" to the
point where I did it without really thinking it through.

The next thing to do, then, is write a function that calculates prime factors.
This I did, and it looks like this.

    {% highlight python %}
    def prime_factors(n):
        if n == 2 or n == 3:
            yield n
            return
        i = 2
        while i * i < n:
            while n % i == 0:
                n /= i
                yield i
            i += 1
        if n > 1:
            yield n
    {% endhighlight %}

What's this? Mutable state? Iteration? This isn't very functional! Well, no. But
it's because I hit one of the big stumbling blocks; no tail call optimisation. I
was blowing the stack up every time. So iteration it had to be.

Once you get used to the idea that iteration is the "Pythonic" way to do things
it isn't too bad, as long as you hide it in a nice function.

So now I have a generator that generates all the possible prime factors in
ascending order, so all I have to do is take the last one.

How do we get the last item in a sequence? We could generate one massive list and
just use a `[-1]` index, but it seems wasteful of memory. A simple reduce that
returns the last value it encounters would take less. So here are a couple of
handy functions that live in `euler.iter`.

    {% highlight python %}
    def first(seq):
        return next(iter(seq))


    def last(seq):
        return reduce(lambda a, x: x, iter(seq))
    {% endhighlight %}

Now we can very easily calculate our answer.

    {% highlight python %}
    from euler.prime import prime_factors
    from euler.iter import last


    def highest_prime_factor(n):
        return last(prime_factors(n))


    def test_0003_prime_factors():
        assert highest_prime_factor(600851475143) == 6857
    {% endhighlight %}

It correctly calculates the answer of *6857* in a far more satisfying *1ms*.
