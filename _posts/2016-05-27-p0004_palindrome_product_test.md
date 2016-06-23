---
layout: post
title: "Problem 4 - Palindrome product"
date: 2016-05-27 14:16:34 +0100 
categories: [python]
---

After the number crunching of problem 3 it seems we're back in the realms of "interview questions from the nineties"
for problem 4.

> A palindromic number reads the same both ways. The largest palindrome made from the product of two 2-digit
> numbers is 9009 = 91 x 99.
> 
> Find the largest palindrome made from the product of two 3-digit numbers.

Seems easy enough. Just need a way of figuring out if something is a palindrome and a way of generating all the numbers
that are the product of two 3-digit numbers. Easy peasy. The naive solution.

{% highlight python %}
def is_palindrome(x):
    s = str(x)
    split = int(len(s) / 2 + 1)
    return s[:split] == s[-split:][::-1]

def palindrome_product(start, end):
    return max(x
               for x in (y * z
                         for y in range(start, end)
                         for z in range(start, y + 1))
               if is_palindrome(str(x))
{% endhighlight %}

Perfect. The right answer pops out in a mere 760ms. Although it does seem a bit slow ...

After much experimentation I have assured myself that, ugly as it is, the `is_palindrome` function is about as fast
as it can be in Python. Walking the string as I would in C is actually slower. The problem is the rest of it. It runs
`(end - start) ** 2` iterations before it even starts to figure out which is highest. That's 810000 iterations.
Just a bit excessive.

After much head scratching and experimenting with partitioning the search domain to try and find the right solution
first rather than having to calculate all of them and call `max`, I determined that it's just not the sort of problem
that lends itself nicely to that sort of optimisation. I had to think of something else.

A plan formed around limiting the search space by counting down from the maximum possible product. By adjusting the
lower bound based on what is possible in subsequent iterations we can stop as soon as we're sure we already have the
highest possible result.

{% highlight python %}
def palindrome_product(start, end):
    lower_bound = start
    pp = 0
    x = end
    while x > lower_bound:
        y = end
        while y > lower_bound:
            r = x * y
            if is_palindrome(r):
                lower_bound = min(x, y)
                pp = max(pp, r)
            y -= 1
        x -= 1
    return pp
{% endhighlight %}

The lower bound limit is adjusted each time we find a palindromic value. It is set to the lower of the two input values,
the idea being that because they can only get lower from here we should stop at this point and never get this low again.

Unfortunately it's a very imperative solution. I built a lovely recursive solution that didn't need to reassign
variables or maintain state at all, but sadly it blew the stack up with every run. The question is whether is can
be done using, as far as possible, generator expressions. The answer appears to be yes.

The first thing to do is write a generator based function that yields a list of candidates.

{% highlight python %}
from itertools import takewhile

def _palindrome_products():
    lower_bound = start
    still_valid = lambda n: n > lower_bound
    for palindrome, new_lower_bound in ((x * y, min(x, y))
                                        for x in takewhile(still_valid, range(end, 0, -1))
                                        for y in takewhile(still_valid, range(end, 0, -1))
                                        if is_palindrome(x * y)):
        yield palindrome
        lower_bound = new_lower_bound
{% endhighlight %}

This will yield a very short list of candidate values by generating candidate values along with a new lower limit.
All we need to do in the horrible imperative bit is use the new lower bound and yield the actual answer. To get the
actual solution to the problem, the highest palindromic number, all we do is wrap the whole thing in a call to `max`.

{% highlight python %}
def max_palindrome_product(start, end):
    return max(_palindrome_products(start, end))
{% endhighlight %}

And there we have it. In practice `_palindrome_products` can be defined inside `max_palindrome_product` to close over
the `start` and `end` arguments. It doesn't have much utility of its own due to the constantly changing lower bound.
 
This is the closest I can get to a non-imperative solution that only has a bit of state attached. It returns the
correct answer of *906609* in a mere *20ms*, so that's better at least.
