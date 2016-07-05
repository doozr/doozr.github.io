---
layout: post
title: "Enough of that"
date: 2016-06-29 11:00:00 +0100
categories: [python,euler]
---
    
I think my interest in [Euler.py](http://github.com/doozr/euler.py) has finally
wained enough to stop updating things. I haven't only done 12 problems, of course,
but I don't have the interest in writing the others up. I just got on with them
and reached my goal of understanding Pythonic things such as generator
expressions and miscellaneous comprehensions.

My main takeaway from it all is that Python is quite a reasonable language to
write things in. It's concise, to the point, and generally has a nice way to
achieve what you're trying to do. Putting too much into comprehensions is bad,
using generators is good, and as expected it's not too difficult to avoid
state by pushing what state there is into generators.

The fact that iterable types and fundamentals such as set, dict and list exist
natively and are so well supported is great. And the fact that even imperative
algorithms that very much follow a *while this do this then this* pattern can
be integrated with a tactical `yield` is great.

There does seem a lot missing though. This may be a philosophical thing; as far
as I can tell Python begrudgingly gives in to functional and list-based 
programming by having the `functools` and `itertools`. Given its dedication to
generators and comprehensions, its (for me) killer feature that allows truly lazy
evaluation of infinite lists to be combined and manipulated, it seems odd.

Python is clearly most of the way there to being a proper list processing type
of language, except perhaps in its disastrously poor support for recursion due
to a lack of tail call optimisation and a tiny stack.

To get the job done on the Euler problems I wrote various helper functions to
lazily generate the various streams of data I needed. Here are a list of iterator
functions I had to write for myself over the course of 46 Euler problems.

* `get_at(n, seq)` - Get the value at offset n of a non-indexed sequence
* `take(n, seq)` - Get the first n values in a sequence
* `take_while_incl(fn, seq)` - Get values from a sequence up to and including the value that fails the predicate
* `every(seq, step, skip=None)` - Get every nth value with interval step, optionally skipping some values
* `window(seq, n)` - Get a sliding window of values over a sequence of width n
* `window_greedy(seq, n)` - Get a sliding window of values over a sequence of widths 1 to n
* `zip_with(fn, *args)` - Map over concurrent values of multiple sequences
* `transpose(xss)` - Transpose a rectangular multidimensional list
* `transpose_irregular(xss)` - Transpose an irregular multidimensional list
* `reverse(xs)` - Reverse any sequence and return as a list
* `scan(fn, xs, a)` - Reduce a sequence using fn and return every intermediate state
* `scan_right(fn, xs, a)` - Same as scan but iterate backwards
* `length(seq)` - Count the length of any arbitrary sequence
* `first(seq)` - Get the first element of sequence
* `last(seq)` - Get the last element of a sequence

At least some of these should, I think, be in `itertools` or a similar
module. Others I didn't need but would come in useful include `head` and `tail` to
yield, respectively, all but the last and all but the first element.

The interesting thing about all these functions is that none of them are
imperative generators. Rather, the are combinations and reuse of existing
iteration functions. It demonstrates how composable such functions are and,
to the same extent, how Python could be a very effective list processing
language using semantically relevant function names rather than having to
recognise patterns in imperative loops.

Have a look at the implementations in [euler/iter.py](https://github.com/doozr/euler.py/blob/master/euler/iter.py)
and marvel at how quickly I got bored with writing docstrings.

On the subject of imperative loops, my next mountain to climb is one that I
have been avoiding for some years now; Go. Everything about its philosophy
rubs me up the wrong way, so it will be interesting to see how I get on.
