---
layout: post
title: "Log For Log's Sake"
date: 2016-09-14 22:00:00 +0100
categories: [go,coding]
---

![I'm a logger and I'm OK](/images/log_for_logs_sake/logging.jpg){:style="float: right;margin-right: 7px;margin-top: 7px;"}

There are a lot of opinions on logging. From 12 factor apps that should only
print directly to stdout to full blown logging frameworks that not only abstract
the concept of inputs, outputs, formats and levels, they even abstract the
concept of *logging frameworks themselves*. Slf4j, I'm looking at you.

Go has an opinionated take on logging which is this: there are only two log
levels; stuff worth logging, and stuff you shouldn't log.

There is a lot to be said for this, but maybe it goes a little too far? Go's
logger has no concept of a log level so anything you log will be logged. It's
good to have that certainty, but it could be nice to have developer-friendly
debug output too.

## On The Level

The traditional log levels can be confusing and muddled, and it's often hard to
decide which log level to use for any given message:

* **FATAL** Something has gone *really* wrong. Doesn't appear when you grep for
*ERROR*.
* **ERROR** Something has gone wrong and you should know about it.
* **WARN** Stuff that's probably more important than *INFO*, but not quite as important as *ERROR*, but nobody wanted to commit either way.
* **NOTICE** For info you still want after turning off *INFO* because somebody
cluttered up *INFO* with *DEBUG* stuff.
* **INFO** Something has happened, and you should know about it.
* **DEBUG** Something of interest to the developer has happened, and they should
know about it.
* **TRACE** Really, really, really, ridiculously low level stuff.

Log levels are often turned off because somebody put too much logging at the
wrong level. If the logging wastes loads of space with repetitive or useless
minutiae then it can get hard to read, so turning off lower log levels can be a
way to combat it. In reality, though, this is evidence that you need to improve
the quality of your log lines or standardise on a smaller set of levels, not
arbitrarily limit quantity.

## So what then?

My take is that the Go way is almost right, but there are actually 3 log levels.
Stuff worth logging, stuff that is only worth logging during development or
debugging, and stuff that is not worth logging. The Go `log` package handles the
first of these, and the last is handled by, well, not logging. But what of the
middle one, the *DEBUG* level?

I don't want to have to install a full-blown logging framework just to be able
to turn on or off what amount to my developers' notes. The built-in Go logger is
perfectly acceptable to me for most of my logging needs. I just need something
that will give me the ability to write debug logs as easily as I can write
normal logs. And so I wrote [jot](http://github.com/doozr/jot).

## Jot

Jot is an additional logger that sits on top of the Go logger (or anything that
can print) and allows me to add log lines that, by default, simply never show
up. It is effectively a log library that, like `log`, only supports one log
level. And in this case that log level is *DEBUG*.

Consider this function.

    {% highlight go %}
    func listen(client Client, ch chan Message,
                done chan struct{}, wg *sync.WaitGroup) {
        jot.Print("Started forwardMessage")
        defer func() {
            jot.Print("Finished forwardMessage")
            wg.Done()
        }()

        for {
            select {
            case <-done:
                jot.Print("forwardMessage detected done channel close")
                return
            default:
                message, err := client.Read()
                if err != nil {
                    log.Print("Error reading from client: ", err)
                    return
                }
                log.Printf(
                    "Received message %d from %s to %s",
                    message.ID, message.From, message.To)
                jot.Printf(
                    "Message id: %d body: %s route: %s",
                    message.ID, message.Text, message.Route)

                ch <- message
            }
        }
    }
    {% endhighlight %}

The log lines are printed as normal so the end user will be able to see when
events come in and if errors occur. But enable `jot` output and the more
detailed information will appear helping to diagnose synchronisation issues and
to see details of the exact data arriving.

If you think Jot is useful have a look at the docs on the [github
project](http://github.com/doozr/jot) and give it a whirl. Any comments, bug
reports, patches or ideas gratefully accepted.
