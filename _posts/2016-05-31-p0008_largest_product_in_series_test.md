---
layout: post
title: "Problem 8 - Larges product in series"
date: 2016-05-31 11:16:48 +0100
categories: [python]
---

Problem 8 presents a new type of problem to solve involving splitting a sequence
into subsequences.

> The four adjacent digits in the 1000-digit number that have the greatest product
> are 9 x 9 x 8 x 9 = 5832.
>
    {% highlight python %}
    73167176531330624919225119674426574742355349194934
    96983520312774506326239578318016984801869478851843
    85861560789112949495459501737958331952853208805511
    12540698747158523863050715693290963295227443043557
    66896648950445244523161731856403098711121722383113
    62229893423380308135336276614282806444486645238749
    30358907296290491560440772390713810515859307960866
    70172427121883998797908792274921901699720888093776
    65727333001053367881220235421809751254540594752243
    52584907711670556013604839586446706324415722155397
    53697817977846174064955149290862569321978468622482
    83972241375657056057490261407972968652414535100474
    82166370484403199890008895243450658541227588666881
    16427171479924442928230863465674813919123162824586
    17866458359124566529476545682848912883142607690042
    24219022671055626321111109370544217506941658960408
    07198403850962455444362981230987879927244284909188
    84580156166097919133875499200524063689912560717606
    05886116467109405077541002256983155200055935729725
    71636269561882670428252483600823257530420752963450
    {% endhighlight %}
>
> Find the thirteen adjacent digits in the 1000-digit number that have the
> greatest product. What is the value of this product?

The key to solving this problem is to take each set of 13 consecutive digits,
calculate the product of them and simply pick the highest. To get each set of
digits and find the product we introduct two new utility functions.

The first is `euler.iter.window` that, as expected, acts as a sliding window on a sequence
(in this case a sequence of characters in a string) and yields each set. It makes
use of the `take` function from problem 7.

    {% highlight python %}
    def window(seq, n):
        if n <= 0:
            raise ValueError("Window size must be positive integer")
        it = iter(seq)
        w = deque(take(it, n), maxlen=n)
        yield list(w)
        for x in it:
            w.append(x)
            yield list(w)
    {% endhighlight %}

The second is `euler.math.product`, a simple wrapper around `reduce` and the
multiply operator to find the product of a sequence.

    {% highlight python %}
    from operator import mul

    def product(seq):
        return reduce(mul, seq, 1)
    {% endhighlight %}

And so to the solution.

    {% highlight python %}
    def largest_product_in_series(seq, n):
        return max(product(int(x)
                           for x in xs)
                   for xs
                   in window(seq, n))
    {% endhighlight %}

When given the 1000 digit number in the form of a string and a window size
of 13 this solution returns the correct answer of *23514624000* in *9ms*.

