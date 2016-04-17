---
layout: post
title: Do not Misuse Code Coverge
comments: True
excerpt_separator: <!--more-->
---

Code coverage is a valuable metric. Software developers write tests for the code they have written and run code coverage analysis which tells them how much of their code is covered by tests or more importantly what parts of their code are untested.

Some organizations and managers make high level of code coverage mandatory for their teams. "90% code coverage and no less!" This is where code coverage goes **wrong**. It’s human nature to optimize our performance according to how we are being measured. [Scott Bain](http://www.netobjectives.com/users/scott-bain), author at Sustainable Test Driven Development [explains](http://www.sustainabletdd.com/2011/12/lies-damned-lies-and-code-coverage.html) it better:

> If developers are writing unit tests because “the boss says so” then they have no real professional or personal motivation driving the activity. They’re doing it because they have to, not because they want to. Thus, they will put in whatever effort they have to in order to increase their code coverage to the required level and not one bit more. It becomes a “tedious thing I have to do to before I can check in my code, period."

<!--more-->

In other words, if coverage is only a target, it could be easily achieved with low quality testing. Good testing require developers to think about the tests they are writing. Forced coverage number takes away that incentive and your team obsesses over the number rather than focusing on the quality, sufficiency and maintainability of tests.

In his excellent article “[How to Misuse Code Coverage](http://www.exampler.com/testing-com/writings/coverage.pdf)”, Brian Marick writes:

> Perhaps this might hint at the answer: when I talk about coverage to organizations that use
85%, say, as a shipping gate, I sometimes ask how many people have gotten substantially
higher, perhaps 90%. There's usually a few who have, **but everyone else is clustered right
around 85%. Are we to believe that those other people just happened to hit 85% and were
unable to find any other tests worth writing? Or did they write a first set of tests, take their
coverage results, bang away at the program until they got just over 85%**, and then heave a
sigh of relief at having finished a not-very-fun job?

## The right way to code coverage

I like the Google [approach](https://docs.google.com/presentation/d/1god5fDDd1aP6PwhPodOnAZSPpD80lqYDrHhuhyD7Tvg/edit#slide=id.g3f5c82004_99_130). They **strive** for 85% code coverage but it is not **“set in stone”** (probably due to the reasons I presented earlier). Expect your team to have high coverage but don't make it a target. Use coverage numbers objectively to improve the quality of tests and the code. Well written tests should have no problem getting 80-90% code coverage. Low coverage numbers ( < 50%) should ring alarm bells and require further investigation.

## Summary

In this post, I talked about code coverage and how making high level code coverage mandatory can be counterproductive. The goal of software testing is not attaining a coverage number. It is to have fewer bugs in production through well thought-out testing.
