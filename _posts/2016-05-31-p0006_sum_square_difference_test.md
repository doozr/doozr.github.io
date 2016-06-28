---
layout: post
title: "Problem 6 - Sum square difference"
date: 2016-05-31 09:14:43 +0100
categories: [python,euler]
---

Problem 6 is really trivial and there's not a lot to say.

> The sum of the squares of the first ten natural numbers is,
>
> 12 + 22 + ... + 102 = 385
>
> The square of the sum of the first ten natural numbers is,
>
> (1 + 2 + ... + 10)2 = 552 = 3025
>
> Hence the difference between the sum of the squares of the first ten
> natural numbers and the square of the sum is 3025 - 385 = 2640.
>
> Find the difference between the sum of the squares of the first one
> hundred natural numbers and the square of the sum.

Here go you.

    {% highlight python %}
    def sum_square_difference(limit):
    sum_of_squares = sum(x * x for x in range(1, limit + 1))
    square_of_sum = sum(range(1, limit + 1)) ** 2
    return square_of_sum - sum_of_squares
    {% endhighlight %}
    
This calculates the answer of *25164150* in *2ms*.
