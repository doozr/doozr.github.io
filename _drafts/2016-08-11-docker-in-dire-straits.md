---
layout: post
title: "Docker in Dire Straits"
date: 2016-08-11 12:00:00 +0100
categories: [docker,devops]
---

Docker has been a big part of my working life for a while now, not because it's
trendy (even though it is) but because it came to my rescue in a sticky
situation. It's been an interesting journey from a technical perspective,
starting with basic images and adding more of the Docker tech stack over time.
But more than that it's been an interesting experience taking a
locked down environment and slowly opening it up to modern ways of working.

# Tunnel Of Love

*The ideal situation.*

So you're going to deploy a new web application. I'm sure there are standard
things that you may do to get that happening. Spinning up VMs in AWS, or in
a private DC. Setting up configuration management, automating build and
deploy processes, adding monitoring and management tools ... all good stuff.

# Money For Nothing

*Paying for a service you could do better yourself.*

But what if none of that was possible?

Instead of spinning up your own VMs, a 3rd party must be engaged to create
each one.

Instead of installing your own OS, the 3rd party installs a managed OS. And
it's CentOS.

Instead of installing the dependencies for your application, the 3rd party must
install them for you, and it can take days or weeks per request.

The servers don't have external network access.

Nor are they accessible via the internal network.

In fact the only way to access them is to use Putty via a Windows VDI with no
centralised access management or even DNS.

And the only way to copy anything on to them is to put it on a
hardware encrypted USB device and transfer it to the Windows VDI via a
virtualised USB interface tunneled over TCP.

And only 2 people are able to do that.

And, to top it all off, the whole thing is hypothetical. None of this exists.
These restrictions are what will exist, once the servers are created, but
there's no ETA.

Pretty disheartening, I'm sure you'll agree.

# So Far Away

*The gulf between our ambitions and our abilities.*

Given that our project was in the very early stages of development, all we knew
was that we had a node.js web application talking to a set of Python services.
We didn't have a concrete list of dependencies, or any idea what the dependencies
were going to be once we'd finished building the thing. We didn't know whether
we'd need build tools on the servers for installing things from `pip` or `npm`.

The problem, then, was that we couldn't tell the 3rd party infrastructure
provider ahead of time exactly what we were going to need 6 months down the
line. We needed to be able to silo the infrastructure completely, reducing
it to a hosted service akin to the "shared hosting" web space packages so
many low cost hosting providers offer.

Another problem is that of accessing dependencies. Our application had dependencies
on a number of external systems. To allow effective building of the application
we needed to build mocks for all those systems, including stubbed HTTP APIs,
databases and even an LDAP service.

# Your Latest Trick

*A way to separate code and infrastructure.*

Enter Docker. A way to package up individual bits of our application and
infrastructure into self-contained, immutable, consistent packages that can be
deployed and run on other machines with relative ease.

The advantages of building immutable, self-contained images that can be run and
managed in the same way are many. You don't need multiple ways to find
and manage log files. You don't need multiple ways to start processes and keep
them running. You don't need multiple ways of specifying configuration. You
don't need multiple ways of managing used and available ports.

And most of all, you don't need to care what the underlying OS looks like as
long as it's running a suitable version of the Docker engine.

# Heavy Fuel

*Changes to development of the application itself.*

The effect on how the application was built was, in fact, not that huge. The
vagrant VM was pretty standard, although we wouldn't normally use CentOS,
being fans of the Ubuntu usually.

## Changes to code

The bigger changes were actually slightly more freedom in development. Thanks
to Docker handling log aggregation and linking containers together for service
access it became very easy to ignore a lot of problems.

* We logged directly to stdout then configured Docker to log to syslog, Splunk
  or Docker's own JSON logger depending on where it was being deployed.
* We deferred deciding where to write files by using mounting host directories
  at runtime depending on the environment
* We allowed Docker to handle port allocation so all instances of a process
  could listen on the same port
* We configured everything else via environment variables
    * External service hosts and ports
    * Access credentials

Being able to defer or avoid considerations such as port allocation, file
destinations and configuration files may not seem like much, but it's an
overhead that simply disappeared from the development process. All of this
could easily be handled at runtime via Docker Compose.

## Changes to the development environment

The development environment gained a cleaner separation between the VM itself
and the software running on it. The VM didn't need rebuilding nearly as much
because most of the configuration was handled inside the Dockerfiles.

Creation of mock services became simplified because they were just additional
Docker images. If they were started and the clients configured accordingly
they'd be used and managed exactly like any other part of the application.

Integration testing, too, became simpler by building an "integration test"
image that, again, was built and managed just like anything else. It connected
to the front-end API and databases directly allowing fixtures to be loaded
and torn down without having to put support for such things directly into
the application codebase.

# Industrial Disease

*Deploying to a multitude of environments.*

The effect on deployment has been nothing but positive. By building the
Docker images as the first step it's possible, by using the mocks and
integration test images, to stand up a complete environment in a few seconds
and a couple of commands that can be fully integration tested. Then the exact
images that were tested can be exported to tarballs and deployed to any
given server.

To make it even easier to work with we made sure the Jenkins jobs only
called scripts within the codebase to perform the build. We wrapped this
up in a Makefile and used the same Makefile whether we were running a
dev build or a Jenkins build. This meant changes to the build pipeline
were tested as soon as they were made before they were even committed.

## Testing with AWS

To test this facility we built a set of AWS images and used the same Ansible
playbooks that configured the VM to configure those. In fact, the homogeny
of the environments was such that the same playbooks were used to configure
the dev VM, the Jenkins build hosts, the AWS test platform and, ultimately,
the real provided VMs.

The only quirk was that we added a "connectivity" tag so that Ansible actions
that required network connectivity did not run on the locked down infrastructure.
Those actions had to be performed by hand.

We did not have a Docker registry. There was no point; there'd be nowhere to
put it where the target hosts and the build server and the developer's machines
could all access it. Rather than trying to force that square peg into our
irregular heptagram of an environment we decided to forego it and just copy
images manually via the `docker save` and `docker load` commands.

We set up auto-deployment to the AWS hosts. We knew it wasn't going to be
possible for the real hosts due to network constraints, but we figured it's
better to have the ability and not use it than not have the ability and, down
the line, having to retrofit it. This gave rise to a very interesting
problem.

## Size matters

Tarring up the docker images and the script used to deploy and stand up
the containers seemed the best option, and it worked well. But it took a long,
long time. Both exporting the images on the build server and the actually copy
over the wire were taking ages because the images were huge.

We were using Debian base images. They start at 100MB before you even add your
code, never mind your dependencies. By the time we had set up our dozen or so
images, installed 400MB of npm packages and 100MB of pip packages and
configured our mock LDAP and Oracle databases we were looking at over a gigabyte
of stuff to transfer over the wire.

Enter Alpine, a tiny Musl libc and busybox based distribution. The base image
for that is only 5MB and, with some clever Dockerfile optimisation and
by ensuring that we only included what we absolutely needed in the final image,
it was possible to reduce the deployable to around a third of the original size.

This problem would not affect users of Docker registry. That only copies the
image layers that have changed or been added when pulling or pushing images.
The network constraints we were facing meant we had to copy the full version of
every image, every time. Not very handy.

## Docker Compose Compose

To be able to stand up the service with the right containers, environment variables,
logger configuration and mounted directories for a given environment we added
a simple shell script to wrap Docker compose. This allowed us to specify
a file containing the configuration required for a given host and run the
relevant docker-compose commands to make it all work.

In fact the script developed over time to be able to do things like override
settings per host, disable or enable custom log drivers, and manipulate
containers at a service, rather than image level.

# Walk Of Life

*Dealing with issues ahead of time.*

There were many issues we encountered while building the application on our
CentOS Vagrant and AWS instances. The Docker version for CentOS at the time
was 1.7 and didn't support everything we needed, so we upgraded to 1.8. We
found a version of that in EPEL but it was ... altered.

The security model was different for CentOS, making it harder to use as
non-root users, locking down what containers could do via SELinux, and
tightly integrating it into Firewalld and Systemd.

Thankfully having all these problems occur on the development VM made them
easier to diagnose and fix.

Other security concerns involved making sure we weren't pulling in weirdness,
hacks or untested new versions. To achieve this we followed standard security
best practice for Docker (at the time; it's changed more recently).

* Do not run processes in containers as root
* Do not pull images by tag; use a hash if you want to always get the same one
* Explicitly specify dependency versions
* Defang SetUID binaries

The new Docker image signing functionality in recent versions should remove the
need to pull by hash, but it's still a good way to make sure you only get the
exact image you expect.

# Twisting By The Pool

*Actually rolling this thing out.*

Once the real VMs started appearing further complications obviously cropped up.
No amount of mocks and testing can prepare you fully for connecting to an
unknown 3rd party databases or services. But thankfully the problems were largely
minimised.

## Deploying to test

Deploying to test brought up a slew of problems related to talking to a
real database. The schema we were testing against wasn't quite right, the
credentials didn't give us all the access we thought they would, and some of
the schemas didn't have aliases we thought they might. But these problems
were fairly easily resolved, and testing was up and running.

## Deploying to staging

Deploying to the staging environment brought fresh challenges again. It
would be the first time we were mounting a real NFS mount into a Docker container
and SELinux reared its ugly head.

It was also the first time we were connecting to a real Splunk cluster, as opposed
to the simple free version we had been using in development. A few misunderstandings
over the difference between a deployment host, a search head and an indexer were
quickly resolved there, too, and all was well.

## Deploying to production

It worked!

Deploying the MVP to the production environment occurred 3 months after
switching to Docker, and they were utterly uneventful. We deployed the same
images as we had already been deploying to test and staging, so we knew the
dependencies were all there.

A couple of months after that the full application was deployed.  We already
knew what Oracle issues we were likely to hit and had sorted them out. It all
worked smoothly, as well it should.

# Sultans Of Swing

*Yes, I've run out of relevant Dire Straits songs.*

Given the limits discussed at the start of this post, I was sure getting this
thing deployed was going to be a nightmare. But necessity is the mother of
invention, and there is no education like adversity.

Looking back, there were things we'd have liked to change, but by and large
we ended up in a good place. Consistent environments, automated build and
(as far as possible) deployment. Simple configuration of services. infrastructure
as code.

Since going live there have been a number of changes for the better. Docker
tooling has improved greatly, including the v2 Docker Compose format and
Docker networking, the improvements to Swarm and 3rd party tooling such as
Rancher that make managing hosts running Docker much simpler.

Internally there have been changes, too. We have been allowed to open a port
to an internal registry so we can push from the build server into that and then
pull from the VMs rather than copying tarballs. We have to do it via 2 reverse
proxies and 3 intermediate registries for purposes of security theatre, but
it's certainly an improvement.

We also have more VMs now so we have two full stacks stovepiped and load balanced,
allowing us to take them down independently. Blue/green deployment is starting
to look like a possibility in a system that, 9 months ago, prevented us from
even copying files.

Things still aren't ideal, but we're deploying consistently at least once a week,
and even that is only held up by internal change process. We're deploying to
the locked down test VMs multiple times daily with no major delays, and every push
to master results in a fresh set of images, fully tested by standing up a
fresh environment for each one.

Things could be a lot worse.
