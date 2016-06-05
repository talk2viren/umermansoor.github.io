---
layout: post
title: Write Less Code
comments: True
excerpt_separator: <!--more-->
---

Not too long ago, I sat down to 'clean up' a project that I inherited. I was given the reins of the refactoring efforts because the project has had several bugs in production. It was stuck in a vicious cycle where fixing old bugs would introduce new ones. So I dived into the source code one weekend and the problem soon became evident: **the project was a big, hairy mess**. I use the word *big* because there was lots of unnecessary, redundant and tightly coupled code. By *hairy mess*, I don't mean that the code looked amateur or was full of shortcuts. In fact, the problem was quite the opposite. **There was too much magic** and everywhere I looked, I saw clever and grandiose design practices that that no relationship with the actual problem that the project was built to solve. Things like reflection, aspect oriented programming, custom annotations were all present. The project was an over-engineered beast. To put it into perspective, after the refactoring was over, the project was reduced to less than half of its original size.

<!--more-->

 I'm sure the developers who wrote the project did so with the best intentions, **but their clever tricks turned against them**. They spent a lot of time on periodic maintenance and fixing bugs. The clients were unhappy that the software was full of bugs. The developers felt like shit because everyone was always complaining about the project. But who's to blame for their misery, for the long hours they had to work to fix the bugs and get no satisfaction out of their jobs? **No one else to blame other than the developers themselves**. One of my favorite bloggers, Jeff Atwood, wrote that the [best code is no code at all](https://blog.codinghorror.com/the-best-code-is-no-code-at-all/):

> It's painful for most software developers to acknowledge this, because they love code so much, **but the best code is no code at all**. Every new line of code you willingly bring into the world is code that has to be debugged, code that has to be read and understood, code that has to be supported. Every time you write new code, you should do so reluctantly, under duress, because you completely exhausted all your other options. Code is only our enemy because there are so many of us programmers writing so damn much of it. If you can't get away with no code, the next best thing is to **start with brevity**.

As developers, we have an itch to come up with clever solutions that we think will make us look professional or help us learn a new tool or technology. We build complex layers to solve simple problems and justify them as being "actually necessary". **But we must realize that the more code we write, the more magic we apply, the more opportunities and doors we leave open for the bugs to creep in**. These bugs will come back and haunt us or our successors in the form of overtime required to fix them on time. I'm obviously not talking about using slick tricks to reduce the number of lines of code. Rather we should ask ourselves whether we need to write all that code to solve the actual problem. I've seen a couple of **custom ORMs and thread pools** in my career which brings me to another point:

> Don't reinvent the wheel. Pretty please.

But don't just stop there. Think whether that fancy framework is needed at all. A project I worked on used [Hibernate](http://hibernate.org/) along with the complementary [DAO](https://en.wikipedia.org/wiki/Data_access_object)'s and [DTO](https://en.wikipedia.org/wiki/Data_transfer_object)'s to execute **one** simple, straight-forward query. Another project had a comprehensive event handling system for a filter that used the [reflection API](https://docs.oracle.com/javase/tutorial/reflect/) to find and invoke the handler class based on the event type. It was an "ingenious" solution and it took me sometime to figure out that the unused methods marked by the IDE were actually invoked using reflection. The icing on the cake: the system handled just one type of event. About five classes worth of code could have been condensed into a simple `if` statement:

```java
if (event.type == THE_ONE_TYPE_THIS_SYSTEM_CAN_HANDLE) {
  process_the_event_and_return_result;
}
```

Our goal should be to keep our solutions as simple as possible and stay away from our natural tendencies to over-engineer, use clever tricks and design patterns until it can be proven that they are absolutely necessary to solve the problem. **Complexity is our worst enemy**. I'm going to end this post with an excellent piece of advice from Jeff:

> If you love writing code-- really, truly love to write code-- **you'll love it enough to write as little of it as possible**.
