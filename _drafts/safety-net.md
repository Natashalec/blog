---
layout: post
title: "Automated Tests Are the Safety Net that Saves You"
date: 2022-06-01
place: Moscow, Russia
tags: testing
description: |
  Modifying the source code and knowing that there are no
  automated tests in place is similar to working on the height
  and knowing that a single mistake could cost you life.
keywords:
  - safety net
  - testing safety net
  - unit testing
  - testing safety
  - why unit testing
image: /images/2022/06/safety-net.jpg
---

Automated tests are the ones usually called unit tests or integrations tests
or just tests that are being executed _automatically_. That's the difference
between them and manual tests. What is the purpose of automated tests?
First and foremost, they reduce the amount of routine work: we don't
need to remember how to test a module, the tests remember. We just click
a button and a suite of tests, which may consist of hundreds or thousands,
runs and reports errors, if they are found. Time saving is important,
but it's not the only and, if you ask me, not the most important purpose
of automated tests. A more important one is their role of a safety net.

<!--more-->

{% jb_picture_body %}

Do you know what a safety net is? Wikipedia
[says](https://en.wikipedia.org/wiki/Safety_net) that
it is "a net to protect people from injury after falling from heights."
You most probably have seen it on the buildings, which are being
constructed or reconstructed.
The red one on the picture is the safety net.

If construction workers accidentally fall down
or drop their instruments, they won't die and won't kill anyone on the ground.
Does this knowledge help them work more effectively? I believe, it does. I didn't
find any research on this subject, which would compare the productivity
of workers who know that there is a safety net beneath them with the productivity
of workers who know that a mistake would most probably cost them their lives or
a life of kid playing on the ground. The results of such a research are obvious,
I believe.

{% youtube Y0Zx_sdVG48 %}

The same is true for us programmers. Modifying the source code and knowing that
there are no automated tests in place is similar to working on the height
without a safety net: one mistake could cost you a server downtime, a frustrated
customer, lost money, and maybe a lost job. How high would be the productivity
of a programmer working like this? Do we need to do a research to find out or the answer
is as obvious as for construction workers?

In order to give you enough confidence, automated tests must not only be
written and committed to a repository, but they must be automatically executed
before every change you are trying to make to the trunk. You must be able
to run them after every change you make locally, incrementally developing the code.
Then, you must know that even if you accidentally forgot to run them locally
and sent your branch to the trunk for merging, the automated merge pipeline
will catch you.

The more time and efforts you invest into making your safety net stronger,
the better your tests cover your code, the more _productive_ will be your
work, the more safety you will feel in introducing new features or fixing bugs.
Simply put, you will code _faster_ and _better_. Just because you will know that
you can't break what you've done yesterday.

The first thing you do when you start a new software project is
the implementation of a safety net: you automate the build pipeline and you create
a few simple automated tests. You make sure they protect you. Only then, you
start writing code. Ask construction workers: are they allowed to start a workday
without a safety net? Obviously, not. I'm sure it's even illegal. The same
must be true for us programmers: coding without a safety net of automated tests
must be illegal.

When a bug is reported to you by your customers, this should mean
that the safety net has a _hole_. It is not tight enough, that's why the bug
felt down on the head of your customer. What do you do before you fix the bug?
You fix the safety net. You add more automated tests, which can catch this
bug in the future. Only then, you fix the bug.

I believe that a safety net of a software project is its most valueable asset.
