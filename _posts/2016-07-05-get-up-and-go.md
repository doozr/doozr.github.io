---
layout: post
title: "Get up and Go(pher)!"
date: 2016-07-05 09:00:00 +0100
categories: [go,euler]
---

I have decided to learn Go. This decision may come back to haunt me.

Make yourself comfortable. This is a long one.

![Gopher It](/images/get_up_and_gopher/gopher.png){:style="float: right;margin-right: 7px;margin-top: 7px;"}

# Why do this to yourself?

Why learn Go? Why not learn Go? Lots of people rave about how amazingly
productive they are, how easy concurrency is, how clean and easy
to read it is, and how great the tooling is.

Other people rave about how much of a regression it is. How it focuses on
coders typing code, not coders getting things done. How it nonchalantly
discards years of programming language development because some people are
just really nostalgic for the 80s.

## I'm so trendy

Over the last few years I've noticed a distinct trend in programming language
development. Well, a couple of trends. The first is that the mainstream (and
less mainstream) ones are becoming more and more alike. Java 8, C#, Swift are
all so similar. And depending on your background you'll see they're similar
also to Scala, or Python, or Ruby, or whatever your poison.

The second trend is that, in fact, many of the problems we've solved in the
last decade only appeared the decade before. Tail call optimisation,
pure functions and stateless development, share-nothing concurrency,
code-as-data, microservices; all these things existed in the 80s. They are
not 00s developments. We just forgot because the 90s were a terrible, terrible
time for software development.

What happened in the 90s? C++, Java and OOP took hold. It was awful.
Good practice thrown out in favour of this "obvious" improvement to software
development. Oh, and XML. Reinventing SGML one feature at a time while
providing none of its elegance. There is a
[wealth](https://blog.pivotal.io/labs/labs/all-evidence-points-to-oop-being-bullshit)
[of](http://harmful.cat-v.org/software/OO_programming/why_oo_sucks)
[information](http://blog.jot.fm/2010/08/26/ten-things-i-hate-about-object-oriented-programming/comment-page-2/)
[about](https://www.leaseweb.com/labs/2015/08/object-oriented-programming-is-exceptionally-bad/)
[why](http://www.smashcompany.com/technology/object-oriented-programming-is-an-expensive-disaster-which-must-end)
[OOP](http://c2.com/cgi/wiki?ArgumentsAgainstOop)
[sucks](http://lambda-the-ultimate.org/node/489).

## That was then ...

So here we are in the 10s. We've learned a thing or two. Old style programming
is making a comeback. Lisp, Clojure, Elm, Elixir, Erlang, Scheme, Haskell, F#
and the like are reclaiming, and sometimes literally reviving, older languages
for the modern age.

Go is the antithesis of OOP. It doesn't do abstraction. At least, not in
the way OOP enthusiasts mean it. It doesn't do code that describes what, only
how. It actively avoids immutability or statelessness. It looks like C with
less of the memory management, and only a small fraction of the keywords.
Sure, it avoids the pitfalls of OOP, but at what cost? It's surely a toy
language built for people who would rather write *more* code, not less, to
get things done?

# How to get started

So given I had decided to subject myself to this throwback of throwbacks,
how was I to get started? First things first, the
[Golang tutorial](https://tour.golang.org/welcome/1). I was impressed by this,
not because of the language it describes necessarily (although a language
that can be described in as few steps as this might be OK) but because I
genuinely found that it was a good introduction to everything. I felt confident
stepping out on my own, with the tutorial to refer back to, and building stuff.

I'll keep a tally, I think. A score for **For** and **Against**. The tutorial
is an immediate point for the **For** camp.

**For** 1 - 0 **Against**

# Troubles with tools

Go has a multitude of tools. It's often said that its tooling is its greatest
asset, along with the tininess of the language. Most of the useful tools are
actually built into the `go` command itself, providing a one-stop shop for
most of your development needs. Some of its features include:

* a build system, no Make/maven/ant nonsense here
* documentation on tap, like pydoc
* a fix tool to upgrade your code to newer API versions automatically
* a package manager
* an installer
* a test runner
* a whole treasure trove of utilities such as coverage generator, profiler,
  tracer, asm and linker

Aside from the `go` command a bunch of standard stuff seems to be included.
Things like an automatic code formatter to ensure you never fail a code
review because you missed some whitespace ever again. An imports manager
that keeps the imports list in trim and readable. And the very useful
Delve debugger that seems to integrate nicely with IDEs and the commandline
alike.

The only major gripe I have is that there is no REPL. Given that the [Go
playground](https://play.golang.org/) exists and you can even run a
presentation with runnable Go snippets build right in with Go Present
it seems an odd omission. Maybe it just doesn't suit a line based REPL
but a block based scratch environment works better? I don't know but
it would be nice to have something that doesn't need me to be online.

Who gets the point, then? I can't help but think it's heavily in the
**For** direction. A lack of REPL is annoying but there are workarounds.
The other stuff is just much nicer than anything to have ever come out of
any Java or C# project ever.

**For** 2 - 0 **Against**

# Project Geuler

To experiment with Go code of my own, and to see how it deals with such
list-based things as pretty much every Euler problem, I created
[Geuler](https://github.com/doozr/geuler). I wanted Euler.go to complement
Euler.py but that caused the compiler to throw a wobbly because it kept
trying to compile directories and binaries as code.

I implemented the first 10 Euler problems just to get a feel for it, and
just for a laugh I made them all run at the same time by running each one
in a goroutine, using a channel to gather the results. I was particularly
pleased with how much faster things working with primes ended up
by running my Sieve Of Eratosthenes prime generator in a background
thread. In fact I was very happy with performance all round.

Overall I was pretty impressed with how concise the code turned out. I
expected it to be sprawling with nonsensical mutable variables being
used for state tracking, and there was a bit of that. But by and large
I found ways around it and only used as many variables as the Python
equivalents. Pretty smart.

No score for this one. The results will fall out over the next few bits.

# Coding style

There is a lot of enforcement in Go. Unused variables and imports
are banned. Tabs are used instead of spaces. Parens are never used
for if and for, but are always used for functions. Curlies are
always used no matter what.

A lot of this stuff I don't mind. In a modern editor, tabs v spaces
is neither here nor there. Curlies are managed automatically. And
I never much cared for parens around ifs.

But ... it uses CamelCase. Everywhere.
EverythingIsSquashedIntoSingleBlocksOfCharacters.
ItIsRidiculouslyHardToReadThem.
IDoNotCareWhatAnyoneSays.

That's a mark Against.

**For** 2 - 1 **Against**

# For the love of loops

For loops. The Go blurb proudly proclaims that Go only supports one
iteration primitive because that's all you'll ever need. Go tell that to
the LISP lot who have no iteration primitives at all.

So for loops then. In practice Go has three types of for loop, so it's a
bit cheeky to suggest that by using the same keyword for 3 types of loop
that they've only got one type of loop. These types are:

* The C `for x:=0; x<0; x++ {}`
* The While-style `for x < something {`
* The Ranger `for ix, x := range someIterableThing {`

The `range` operator in particular is interesting to me. It appears to
create, presumably with a closure, an iterator and each run through the
loop it will act as a call to `next()` on the iterator. Once all the
things in the iterable things are exhausted, the loop ends. It returns
two values, one of which is the index. If you don't want the index,
you have to assign it to `_`. It feels messy. Also I constantly forget
and end up iterating over indices of arrays rather than values.

So it does support some sort of list processing. All the talk I heard
made me think I'd need to have loads of index variables littering my
code for every loop I build.

But consider if you didn't have to build a loop for everything. Even
now, writing this post after a fairly decent size Go project, I am
fed up of writing for loops. It would be lovely not to have to. Especially
when I find that I'm writing the same basic three a lot:

    {% highlight go %}
    // total = reduce(operator.mul, inputs, 1)
    total := 1
    for _, x := range inputs {
        total *= x
    }

    // doubled = [x * 2 for x in inputs]
    doubled := make([]int, len(inputs))
    for i, x := range inputs {
        doubled[i] = x * 2
    }

    // filtered = [x for x in inputs if x % 2 == 0]
    filtered := make([]int, 0)
    for _, x in range inputs {
        filtered = append(filtered, x * 2)
    }
    {% endhighlight %}

And of course I can combine them ...

    {% highlight go %}
    // doubled_evens = (x * 2 for x in inputs if x % 2 == 0)
    // total = reduce(operator.mul, doubled_evens, 1)
    total := 1
    for _, x := range inputs {
        if x % 2 == 0 {
            total *= x * 2
        }
    }
    {% endhighlight %}

I'm sure that this is entirely subjective. Some will prefer the commented out
Python because it describes *what* it's trying to achieve:

    double the value of each even number in input
    find the product of the doubled evens

whereas the Go describes *how* it achieves it:

    for every value in range check if it is even
    if it is even multiply the total by double the value

I don't think there will ever be consensus on which is better but my
personal preference is the former. Also note that the Python version
uses a generator expression for lazy evaluation so the list of doubled
even inputs never actually exists. It just gets generated on demand.

Of course, we could write it to use recursion so we at least avoid
mutable state:

    {% highlight go %}
    func ProductOfDoubledEvens(inputs []int) int {
        if len(inputs) == 0 {
            return 1
        }

        if inputs[0]%2 == 0 {
            return (2 * inputs[0]) * ProductOfDoubledEvens(inputs[1:])
        } else {
            return ProductOfDoubledEvens(inputs[1:])
        }
    }
    {% endhighlight %}

It ends up slightly longer, and just as single purpose. More on that later.

**For** 2 - 2 **Against**

# Generically grim

The question, then, is why we have to use for loops for every sort
of iteration. I mean, we could use recursion, and as demonstrated it
would remove mutable state for at least some classes of problem. But
what about general purpose solutions.

Consider this simple implementation of reduce:

    {% highlight go %}
    func reduce(fn func(int, int) int, inputs []int, accumulator int) int {
        if len(inputs) == 0 {
            return accumulator
        }
        return fn(inputs[0], reduce(fn, inputs[1:], accumulator))
    }
    {% endhighlight %}

Looks good, right? Wrong. It has a subtle bug. It only works for
commutative operators. If `fn` depends on the order of the list to get
the right answer, it will be wrong because it actually iterates backwards.

A better version is:

    {% highlight go %}
    func reduce(fn func(int, int) int, inputs []int, accumulator int) int {
        if len(inputs) == 0 {
            return accumulator
        }
        return reduce(fn, inputs[1:], fn(accumulator, inputs[0]))
    }
    {% endhighlight %}

See the difference? I said it was subtle. And that's the problem with
roll-your-own. It's very easy to make very basic mistakes. You want
a decent standard library to do this for you.

But how can it? What if you want to do something that works with strings,
or floats, or GargleBlasters? What if you want your inputs to be
GarbleBlasters and your output be a BowlOfPetunias? Well, you can't.
You have to be explicit about what types you accept and what types you
return right from the off, else use `interface{}` and lose all type safety.

The problem is that of generics. To write a truly generic version of
reduce you need to be able to specify input type on a generic interface,
and output type on the function itself. Like this.

    {% highlight go %}
    func reduce<I, O>(fn func(O, I) O, inputs iterable<I>, accumulator O) O {
        if len(inputs) == 0 {
            return accumulator
        }
        return reduce(fn, tail(inputs), fn(accumulator, head(inputs)))
    }
    {% endhighlight %}

`head()` and `tail()` would return the first, and all but the first,
elements respectively.

The code above is not valid Go, and it is not possible to have generic
functions. Everything must be utterly bespoke to the task at hand, so
everything must be written over and over for each new situation.

But let's look on the bright side; if you write a custom reduce function
for exactly one scenario, you can give it a nice semantic name to make
the calling code more readable!

**For** 2 - 3 **Against**

# Digging channels

It's time for something new, now. Channels. Channels are the pièce de résistance
of the Go toolbox. They are strongly typed, buffered, blocking conduits of
information to pass between goroutines, the other half of Go's fabled
concurrency genius.

I had a play with channels with the Geuler project and they worked pretty well.
I have used Ruby's Queue to great effect and as far as I can see they are pretty
much identical to them in practice. A thread-safe way to pass information between
concurrent function calls with blocking and buffering to allow control to go
where it needs to.

So they're not really anything new. That's fine, nothing in Go is particularly
new, I think. Even goroutines are just syntactic sugar around threads. But
don't knock it; having channels and goroutines with such accessible syntax
makes them very easy to use. It lets responsibility be handed off very safely.

Dubious example time:

    {% highlight go %}
    func thinDoerDispatcher(incoming <-chan int, outgoing chan<- string) {
        for i := range incoming {
            outgoing <- doLongRunningThing(i)
        }
    }
    {% endhighlight %}

This describes a read-only channel of integers, a write-only channel of strings,
and a potentially long running function in the middle. The function, then, is
totally separated from the rest of the program and is allowed to do its thing
completely without concern for the synchronisation of the threads.

My only issue with channels is that it's very easy to accidentally deadlock
the program and cause a panic by exhausting a channel and not closing it.
Having to manually close a channel is something Ruby Queue or Python yield
users just don't have to worry about.

That said, goroutines and channels are nice. I do like them.

**For** 3 - 3 **Against**

# Super-generators!

On the subject of channels, I discovered a nice pattern to use channels as
a form of generator while manipulating lazily evaluated, potentially infinite
sequences.

The trick is to return a channel that is written to by a goroutine that
closes over it. This puts control of closing the channel in the control of
the function that created it.

    {% highlight go %}
    // This generator is infinite
    func Fibonacci() (fib chan int) {
        fib = make(chan int)
        go func() {
            x := 0
            y := 1
            fib <- x
            for {
                x, y = y, x + y
                fib <- x
            }
        }()
        return
    }

    // This generator doesn't know if it's infinite or not
    func Double(input chan int) (doubled chan int) {
        doubled = make(chan int)
        go func() {
            for x := range input {
                doubled <- x * 2
            }
        }()
        return
    }

    // This generator is not infinite
    func Take(n int, ch chan int) (taken chan int) {
        taken = make(chan int)
        go func() {
            for x := 0; x < n; x++ {
                // Do you like my double arrow?
                taken <- <- ch
            }
            close(taken)
        }()
        return
    }

    // This converts a finite channel to an array
    func ChanToArray(ch chan int) []int {
        a := make([]int, 0)
        for x := range ch {
            a = append(a, x)
        }
        return a
    }

    func main() {
        fmt.Println(ChanToArray(Take(10, Double(Fibonacci()))))
        // Output: [0 2 2 4 6 10 16 26 42 68]
    }
    {% endhighlight %}

And there we have it. Infinite sequences, potentially infinite mappers,
a filter and a way to close the whole thing down. And thanks to the `range`
operator I didn't need to manually close except the one with a hard limit.

The syntax is a bit ugly, to be fair. An anonymous function that has to be
called immediately doesn't seem great. However, given that each of the
channels could be easily buffered to allow background generation of values,
potentially on a different CPU, it could be nice from a concurrency point of
view.

So does Go deserve another point for being able to do this? Oh, go on then.

**For** 4 - 3 **Against**

# Qbot

For my next trick, and to start to get more of a feel for how Go performs
in real world scenarios, I thought I'd try something useful. So I built
[Qbot](https://github.com/doozr/qbot), a Slack bot for managing contended
resources in development teams (for example, merge tokens).

It has a lot more stuff in it. Fewer channels, interestingly, but the ones
it does use it uses for good effect, keeping the bot responses snappy and
not held up by unrelated activity. Updating the user cache, for example,
or serialisation, does not slow down message processing.

The Queue type is designed to be immutable, always returning a copy from the
various associated functions. Of course, if you dig into the type and manually
set values then there's nothing that can be done to stop you because Go
fundamentally doesn't support immutable values.

# Test it out

Testing in Go is, as everything else, stripped down. It has a test runner,
and it's got a couple of extra tricks up its sleeve. It has three modes,
test, benchmark and example.

* The test mode is designed to allow unit testing. Like Python the test files
  live alongside the source code rather than in a separate structure. I have
  grown to like this technique.
* The benchmark mode is very similar except that it is used for benchmarking
  activities. Given a function that does a thing, a value is passed in to
  tell it how many times to run. This number is adjusted automatically until
  the times can be calculated reliably.
* The example mode is a bit like the test mode, but allows the test to be run
  as a small program that outputs to stdout. A comment-based assertion then
  describes what the output should look like. This is compared at test time
  and an assertion is raised if it's wrong.

Between these three modes there's a lot to go at, although I don't really
understand the purpose of the example type. Maybe they get included
in documentation? I will have to have a play with it.

The main gripe I have with testing is that there is no built-in assert
function. Unlike every single other language in the world, where you
would do something like this:

    {% highlight go %}
    assert(expected == actual, "Should have been " + actual)
    {% endhighlight %}

you actually do this:

    {% highlight go %}
    if expected != actual {
        t.Errorf("Should have been %s", actual)
    }
    {% endhighlight %}

Is it the end of the world, having to type an if every time? Maybe not, but
it doesn't half make the tests look a mess. That said, it also forced me
to wrap basic asserts in more semantically meaningful helper functions.
So, instead of:

    {% highlight go %}
    assert(len(arr) == 1 && arr[0] == value)
    {% endhighlight %}

I could instead use:

    {% highlight go %}
    assertWrappedInArray(arr, value)
    {% endhighlight %}

A lot more useful.

I'm a bit torn on this. The craptacular attitude of "it's not hard to write
more code so why not just do it" is very evident. But on the other hand
all this is built in and so provides zero opportunity for mucking up. And
it does so little that it can be customised to the Nth degree.

I'll give a point to **For**.

**For** 5 - 3 **Against**

# Bound functions

Bound functions are the Go equivalent of methods. Go doesn't have classes
as such, and certainly doesn't have things like inheritance or overloading
or any of that OOP nonsense. That's a plus point right away. But it does
have bound functions that it refers to as methods. What are they then?

Consider this:

    {% highlight go %}
    type MyCounter int

    func Increment(c MyCounter, n int) MyCounter {
        return c + MyCounter(n)
    }

    c := MyCounter(2)
    c = Increment(c, 3)
    // c == 5
    {% endhighlight %}

Fairly trivial. But what about being able to tell the MyCounter variable to
do the increment?

    {% highlight go %}
    type MyCounter int

    func (c MyCounter) Increment(n int) MyCounter {
        return c + MyCounter(n)
    }

    c := MyCounter(2)
    c = c.Increment(3)
    // c == 5
    {% endhighlight %}

Not the most exciting example, but indicative of what methods are in Go. Just
functions that happen, by coincidence, to be called in a postfix position on
variables of a given type.

It also supports pointers (more on that later) so it is possible to pass
by reference and update in place. An in-place updating int.

    {% highlight go %}
    type MyCounter int

    func (c *MyCounter) Increment(n int) {
        *c += MyCounter(n)
    }

    c := MyCounter(2)
    c.Increment(3)
    *c == 5
    {% endhighlight %}

Of course, my feelings on mutable state are well known, but it's possible.
If the MyCounter type was a struct rather than an int, it's possible that
holding a pointer to a bit of state could allow us to silo the management
of that state into a single package.

Do I think that mutable state is bad? Yes. Do I think it's unavoidable in
many circumstances, especially when communicating with the outside world,
a place that is made of nothing but state? Yes.

So I'm glad there is a way to handle passing by reference and updating in
place. It can come in handy, and it's a relatively low-touch way to do it.
The pointers aren't like C pointers. There's no arithmetic. They're more
like C++ or Java references, the difference being that, like C pointers,
a `*int` is a different type to an `int` and converting between them is
very explicit, like everything else in Go.

**For** 6 - 3 **Against**

# Codes and Packages and Modules oh my!

Not much to say on code layout. It uses packages. Like everything else,
the layout is prescriptive if you want to work within the ecosystem, but
you can break out if you want. Nothing is set in stone, but there is a
lot of convention around the build tooling.

It's not the worst layout, but the weird `$GOPATH` thing where all of
your Go projects and all of their dependencies exist in one gigantic
directory structure seems weird. And having your public facing source control
in the project name is just bizarre. That said, it makes managing it easier.

I just think they missed a trick by not calling it `$GOHOME`.

**For** 7 - 3 **Against**

# Philosophising

The philosophy of Go is very nicely summed up in the FAQ on
[golang.org]():

> ### What are the guiding principles in the design?
>
> Programming today involves too much bookkeeping, repetition, and clerical work.
> As Dick Gabriel says, “Old programs read like quiet conversations between a
> well-spoken research worker and a well-studied mechanical colleague, not as a
> debate with a compiler. Who'd have guessed sophistication bought such noise?”
> The sophistication is worthwhile — no one wants to go back to the old languages
> — but can it be more quietly achieved?
>
> Go attempts to reduce the amount of typing in both senses of the word. Throughout
> its design, we have tried to reduce clutter and complexity. There are no forward
> declarations and no header files; everything is declared exactly once.
> Initialization is expressive, automatic, and easy to use. Syntax is clean and
> light on keywords. Stuttering (`foo.Foo* myFoo = new(foo.Foo)`) is reduced by simple
> type derivation using the `:=` declare-and-initialize construct. And perhaps most
> radically, there is no type hierarchy: types just are, they don't have to announce
> their relationships. These simplifications allow Go to be expressive yet
> comprehensible without sacrificing, well, sophistication.
>
> Another important principle is to keep the concepts orthogonal. Methods can be
> implemented for any type; structures represent data while interfaces represent
> abstraction; and so on. Orthogonality makes it easier to understand what happens
> when things combine.

A very worthy cause, I think. Languages do tend to accrete rather than acquire,
to kludge rather than design, to try and add feature ungracefully. And I think
the designers are right, the language does need to be small and expressive, not
large and complex and confusing like, for sake of argument, Scala.

And given that the language is built for systems programming where type safety
is very important, I think it's a good trade off.

Of course within each community trends crop up. I've seen arguments along these
lines regularly while looking for Go related hints and tips:

> No, Go doesn't need iterators; for loops are easy to write

> No, Go doesn't need assertions; ifs are easy to write

> No, Go doesn't need a bigger standard library; functions are easy to write

> No, Go doesn't need generics; multiple implementations for each type are easy to write
dd

And, my favourite, paraphrased from a comment in a Google Group chat some
time ago when asked why Go doesn't support aforementioned generics and list
processing primitives:

> Anyone would think that coders weren't being paid to code. Of course they are!
> That's why Go gives coders the ability to write lots of code. That's what
> they are for! Just use for loops and stop complaining!

I'm sure the attitude issues of the community (let's face it, IT communities
have a well deserved terrible reputation) are not shared by the Go team,
but that's what you see on Stack Overflow. But it's no worse than Apple
zealots who simply can't comprehend *why* you'd need a feature that Steve had
obviously removed for a *very good if entirely unfathomable reason*.

I might have to give this one to the "For" camp.

**For** 8 - 3 **Against**

# Personal Opinion

So this is Go. A programming language. It has a lot going for it, for sure.
The fact I learned the whole thing, including weirdness like tags on
structs when parsing JSON, in about 3 hours is impressive. And being able to
grok the type system and the use of channels is minutes is, I'm sure,
a credit to the language and tooling more than my addled brain.

And as much as it pains me to say it, I've actually quite enjoyed coding
with it. The [Qbot](https://github.com/doozr/bot) was thrown together in
a very rough form with hardly any thought to structure and, slowly, over a
day or two, honed into what I think is pretty good shape.

And the weird thing is, tiny as the language and standard library may be,
I implemented the whole thing without any third party packages at all. The
only thing I used outside the standard library was `golang.org/x/net/websocket`;
a first party extension.

I was even happy with the Euler implementations, annoyingly, after
agonising so long over the Python ones.

Other things I like about Go include:

* Static compilation so easy to deploy
* Built-in cross-compilation that's just an env var away
* Fast enough compilation that you forget it's not interpreted
* Type system that mostly gets out of the ways

**For** 9 - 3 **Against**

# Concussion

This would be a conclusion but after this pointless waffle for 5000 words I
think concussion fits better. The conclusion is that despite many, many
misgivings I actually quite like Go. I like how it just gets out of the way.

I used to think this of Javascript. Looking back I see that was just because
Javascript is not as bad as PHP. I quite like Python, but concurrency is
a pain and for modern systems programming it's a must. I know there are...
things in Python 3 to simplify, but I've used them and frankly channels are
easier. And finally I've used Ruby for loads of highly concurrent stuff and
quite like it, but it's extra-OOPiness and the fact that it's just so
easy to get into a complete and twisted mess put me off.

Am I conceding to actually liking Go? Like, a lot?

Might be. What of it?
