---
layout: post
title: Automated Tests Help Developers Sleep Better
comments: True
excerpt_separator: <!--more-->
---

In [Pragmatic Programmer](https://www.amazon.com/Pragmatic-Programmer-Journeyman-Master/dp/020161622X), Andy and Dave wrote:

> Most developers hate testing. They tend to test gently, subconsciously knowing where the
code will break and avoiding the weak spots. Pragmatic Programmers are different. **We are
driven to find our bugs now, so we don't have to endure the shame of others finding our
bugs later**.

There hasn't been any shortage of literature on the benefits of automated testing in the last 10-15 years. Yet it comes as a surprise to me that most developers still don't like to write unit tests. Some managers [force developers to write tests by making an arbitrary code coverage number mandatory](http://codeahoy.com/2016/04/16/do-not-misuse-code-coverage/) for releases. This is dangerous because the result is often low quality and ineffective tests that do not catch the bugs that they were supposed to.

<!--more-->

But why do some developers dislike testing? One possible explanation is that they don't buy the idea. Software developers are very smart people and they cannot be coerced into accepting an idea just because management thinks its great. In their defense, the management often fails to coach or convey the idea that **automated tests benefit developers the most**: they are written *by* the developers, *for* the developers themselves. They help developers sleep better at night.

Automated tests are very good at catching bugs before the code is released - *not all the bugs, but most of them*. **Software developers, whether on-call or not, are on the hook for any bugs in their code. When there's a bug that affects millions of users, it's the developers who have to get out of their beds at 2am or stay late evenings to provide a fix**. Not to mention the embarrassment of introducing a bug that irritated thousands/millions of users or resulted in lost revenue. It takes a long time to find the root cause just because at the time, a developer failed to take a break from implementing the functionality to think about all the ways it could break and write good tests to ensure that the bug was caught before production release.

Developers must be driven to test the *sh***t* out of their code. We must put a comparable (sometimes more) effort into writing tests as we do on the actual feature itself. [Different testing techniques](http://codeahoy.com/2016/07/05/unit-integration-and-end-to-end-tests-finding-the-right-balance/) should be used to catch different types of bugs.

> Finding bugs is somewhat like fishing with a net. We use fine, small nets (unit tests) to
catch the minnows, and big, coarse nets (integration tests) to catch the killer sharks.

Pragmatic programmers don't just stop with unit, integration or end-to-end tests. They also load test with 10x the peak traffic to catch the killer whales (even this [wasn't enough for Niantic](http://www.shacknews.com/article/96998/pokemon-go-had-10x-more-users-at-launch-than-the-expected-worst-case-scenario)). They test their system by replaying and replicating actual production traffic patterns. They perform [monkey testing](https://en.wikipedia.org/wiki/Monkey_testing) seeing if their system crashes. **They work very hard to break their own code, motivated by the desire to minimize the number of times they'll have to be rocketed out of sleep or spend their weekend to find and fix bugs in their code**.
