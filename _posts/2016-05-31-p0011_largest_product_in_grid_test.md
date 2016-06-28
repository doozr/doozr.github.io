---
layout: post
title: "Problem 11 - Largest product in grid"
date: 2016-05-31 17:34:12 +0100
categories: [python]
---

Problem 11 brings the most interesting bit of list processing so far.

> In the 20x20 grid below, four numbers along a diagonal line have been marked.
>
    {% highlight python %}
    08 02 22 97 38 15 00 40 00 75 04 05 07 78 52 12 50 77 91 08
    49 49 99 40 17 81 18 57 60 87 17 40 98 43 69 48 04 56 62 00
    81 49 31 73 55 79 14 29 93 71 40 67 53 88 30 03 49 13 36 65
    52 70 95 23 04 60 11 42 69 24 68 56 01 32 56 71 37 02 36 91
    22 31 16 71 51 67 63 89 41 92 36 54 22 40 40 28 66 33 13 80
    24 47 32 60 99 03 45 02 44 75 33 53 78 36 84 20 35 17 12 50
    32 98 81 28 64 23 67 10'26'38 40 67 59 54 70 66 18 38 64 70
    67 26 20 68 02 62 12 20 95'63'94 39 63 08 40 91 66 49 94 21
    24 55 58 05 66 73 99 26 97 17'78'78 96 83 14 88 34 89 63 72
    21 36 23 09 75 00 76 44 20 45 35'14'00 61 33 97 34 31 33 95
    78 17 53 28 22 75 31 67 15 94 03 80 04 62 16 14 09 53 56 92
    16 39 05 42 96 35 31 47 55 58 88 24 00 17 54 24 36 29 85 57
    86 56 00 48 35 71 89 07 05 44 44 37 44 60 21 58 51 54 17 58
    19 80 81 68 05 94 47 69 28 73 92 13 86 52 17 77 04 89 55 40
    04 52 08 83 97 35 99 16 07 97 57 32 16 26 26 79 33 27 98 66
    88 36 68 87 57 62 20 72 03 46 33 67 46 55 12 32 63 93 53 69
    04 42 16 73 38 25 39 11 24 94 72 18 08 46 29 32 40 62 76 36
    20 69 36 41 72 30 23 88 34 62 99 69 82 67 59 85 74 04 36 16
    20 73 35 29 78 31 90 01 74 31 49 71 48 86 81 16 23 57 05 54
    01 70 54 71 83 51 54 69 16 92 33 48 61 43 52 01 89 19 67 48
    {% endhighlight %}
>
> The product of these numbers is 26 x 63 x 78 x 14 = 1788696.
>
> What is the greatest product of four adjacent numbers in the same direction
> (up, down, left, right, or diagonally) in the 20x20 grid?

Lots of fun to be had in this one, twisting the grid around this way and that.
We can re-use the `window` function from problem 8 to get a sliding list of
values over each row easily enough. We just have to manoeuvre the grid to present
all the possible sets of four.

## Horizontal rows

The first lot are easy enough; just call window along each of the rows
of the grid exactly as is. Start in the upper left with `[8, 2, 22, 97]`
and take it from there.

    {% highlight python %}
    window(grid, 4)
    {% endhighlight %}

## Vertical columns

The next thing to do is get the values down the columns of the grid.
For this we need to flip it along its diagonal to bring the verticals
into the horizontal. A new function, then; `transpose`.

    {% highlight python %}
    def transpose(xss):
        return zip(*xss)
    {% endhighlight %}

The results are simply flipping each vertical into the horizontal.

    {% highlight python %}
    >>> list(transpose([[1,2,3],
                        [4,5,6],
                        [7,8,9]]))
    [(1, 4, 7),
     (2, 5, 8),
     (3, 6, 9)]
    {% endhighlight %}

Combined with the window function we can check the groups of four in the verticals.

    {% highlight python %}
    window(transpose(grid), 4)
    {% endhighlight %}

The next bit is where it gets really interesting; a sliding window across
the diagonals.

## Diagonals

Calculating the diagonals turned out to be pretty tricky. The basic issue is that
for each half of the grid, split diagonally from top left to bottom right, there
are two sets of values that need to be retrieved.

To get the first set, we strip in incrementing number of values from the start
of each consecutive row, then transpose the result, like this.

    {% highlight python %}
    1 2 3 4        1 2 3 4       1 6 1 6
    5 6 7 8  -->   6 7 8    -->  2 7 2
    9 0 1 2        1 2           3 8
    3 4 5 6        6             4
    {% endhighlight %}

As you can see, this produces the centre diagonal line and the three lines
above it toward the top right corner.

To get the other half, we need to similarly remove some values, but this time
by reversing the row first and removing the most from the first row.

    {% highlight python %}
    1 2 3 4       1             1 6 1 6
    5 6 7 8  -->  6 5      -->  5 0 5
    9 0 1 2       1 0 9         9 4
    3 4 5 6       6 5 4 3       3
    {% endhighlight %}

Note that the first row is the same for both sets of diagonals. We can simply
ignore the first row for the first set of diagonals.

To perform this feat of stripping and transposing I needed to create quite a
few utility functions to give clear meaning to the expressions. Here they are.

### zip_with(fn, *seqs)

Zip all the sequences and map the tuples with a function.

    {% highlight python %}
    def zip_with(fn, *args):
        return (fn(*xs) for xs in zip(*args))

    >>> zip_with(lambda x, y: x + y, [1,2,3], [4,5,6])
    [5, 7, 9]
    {% endhighlight %}

### reverse(seq)

Like the built-in `reversed` function, but works on any sequence.

    {% highlight python %}
    def reverse(xs):
        return list(xs)[::-1]

    >>> reverse(range(0, 10))
    [9, 8, 7, 6, 5, 4, 3, 2, 1, 0]
    {% endhighlight %}

### transpose_irregular(seq)

Like `transpose` but do not truncate to the shortest sub-array. Instead fill in
blanks with `None`.

    {% highlight python %}
    def transpose_irregular(xss):
        return zip_longest(*xss, fillvalue=None)

    >>> transpose_irregular([[1,2,3,4,5,6],
                             [4,5,6],
                             [7,8,9,0]])
    [[1, 4, 7],
     [2, 5, 8],
     [3, 6, 9],
     [4, None, 0],
     [5, None, None],
     [6, None, None]]
    {% endhighlight %}

### filter_none(seq)

Remove all entries in a sequence that are None.

    {% highlight python %}
    def filter_none(xs):
        return (x for x in xs if x is not None)

    >>> filter_none([1, 2, 3, None, None])
    [1, 2, 3]
    {% endhighlight %}

With this small arsenal of utilities I can start putting together the
diagonal generator.

    {% highlight python %}
    def diagonals(xss):
        return chain(
            (filter_none(xs) for xs in transpose_irregular(strip_tails(xss))),
            islice((filter_none(xs) for xs in transpose_irregular(strip_heads(xss))), 1, None)
        )


    >>> diagonals([[1, 2, 3, 4],
                   [5, 6, 7, 8],
                   [9, 0, 1, 2],
                   [3, 4, 5, 6]])
    [[1, 6, 1, 6]
     [2, 7, 2]
     [3, 8]
     [4]
     [5, 0, 5]
     [9, 4]
     [3]]
    {% endhighlight %}

And there they are.

Of course, there are the other set of diagonals, the ones that run bottom
left to top right. These are generated in the same way but with the input
array reversed.

    {% highlight python %}
    def diagonals(xss):
        return chain(
            (filter_none(xs) for xs in transpose_irregular(strip_tails(xss))),
            islice((filter_none(xs) for xs in transpose_irregular(strip_heads(xss))), 1, None)
        )


    >>> diagonals(reverse(xs) for xs in [[1, 2, 3, 4],
                                         [5, 6, 7, 8],
                                         [9, 0, 1, 2],
                                         [3, 4, 5, 6]])
    [[4, 7, 0, 3]
     [3, 6, 9]
     [2, 5]
     [1]
     [8, 1, 4]
     [2, 5]
     [6]]
    {% endhighlight %}

## Calculating the products

We now have enough to generate a full list of inputs for the window function
using rows, columns and diagonals in both directions. The next step is to take
each set of inputs and convert the lists to a set of products using the `window`
and `product` functions. We can use the `itertools.chain` function to convert
the various input sequences into one long set of inputs.


    {% highlight python %}
    from itertools import islice, count, chain
    from euler.iter import window, zip_with, transpose, transpose_irregular, reverse
    from euler.math import product


    def strip_tails(xss):
        return zip_with(lambda xs, n: reverse(islice(xs, 0, n)), xss, count(1))


    def strip_heads(xss):
        return zip_with(lambda xs, n: islice(xs, n, None), xss, count())


    def filter_none(xs):
        return (x for x in xs if x is not None)


    def diagonals(xss):
        return chain(
            (filter_none(xs) for xs in transpose_irregular(strip_tails(xss))),
            islice((filter_none(xs) for xs in transpose_irregular(strip_heads(xss))), 1, None)
        )


    def rolling_product(xss, n):
        windows = chain.from_iterable(window(xs, n) for xs in xss)
        return (product(w) for w in windows)


    def largest_product_in_grid(xss, n):
        return max(rolling_product(
            chain(
                xss,
                transpose(xss),
                diagonals(xss),
                diagonals([reverse(xs) for xs in xss])
            ), n))
    {% endhighlight %}

This solution produces the correct answer *70600674* in *3ms*.
