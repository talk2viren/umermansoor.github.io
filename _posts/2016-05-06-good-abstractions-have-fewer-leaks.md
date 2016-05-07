---
layout: post
title: Good Abstractions Have Fewer Leaks
comments: True
excerpt_separator: <!--more-->
---

> Abstraction is one of the greatest visionary tools ever invented by human beings to imagine, decipher, and depict the world. - Jerry Saltz

Abstraction are all around us. We abstract things to hide details making it easier to see the "big picture" and help cope with the complexity. When I push down on the gas pedal, a lot of magic happens under the hood to move my car. But I don't have to know any of that. I only ever need to know that pushing on the pedal increases the speed and releasing it will decrease it. The gas pedal is a **simple interface and that's what makes it so great**. It has been with us for a very long time and it's here to [stay](https://forums.teslamotors.com/forum/forums/what-term-should-we-use-gas-pedal).

<!--more-->

In software, like any other engineering discipline, abstractions are everywhere: the protocols, the frameworks, the libraries, the game engines, the file systems, even the programming languages we use everyday are abstractions of [low-level languages](https://en.wikipedia.org/wiki/Low-level_programming_language). It can be argued that everything in computer science is inevitably an abstraction of something more complicated. The higher-level abstractions provide useful functionality in the form of a black-box, **hiding all the complexity and implementation details**.

Except that it's not always so black and white. **Software abstractions are never perfect** and, arguably, are far from perfect. Abstractions are deficient when you have to **understand the low-level details or peek under the covers to understand what's going**. Consider Java's garbage collection process. It would have been great if I never had to think about how it works - after all, it was supposed to work like magic. But in reality, when a bug was reported that **"requests are taking a lot longer to process"**, I had to go in and figure out that the garbage collection was slowing things down. One cannot optimize performance and avoid penalties without learning how the garbage collector works (at least not for high-throughput, low-latency applications). Joel Spolsky called this the [law of leaky abstractions](http://www.joelonsoftware.com/articles/LeakyAbstractions.html):

>  All non-trivial abstractions, to some degree, are leaky.
>
> Abstractions fail. Sometimes a little, sometimes a lot. There's leakage. Things go wrong. It happens all over the place when you have abstractions.

Joel is right; all abstractions in software are leaky. But we also can't live without abstractions. They key is to keep the leakage to a minimum and build good abstractions. Over the years, I have seen both good and terrible abstractions. Not in distant memory, I had to fix an "abstraction" that failed to encapsulate the functionality it provided and had very tight coupling with the applications that used it. 30% of the module's logic was scattered outside of it, weaved throughout other applications using java annotations. Another time I was reviewing some code and came across some very complex logic. It turned out that the developer that created an "abstraction" over Hibernate to provide an even more general and completely useless solution. Hibernate is already an abstraction over SQL! Adding another layer on top of Hibernate made things even more complex than they needed to be. As [David J. Wheeler](https://en.wikipedia.org/wiki/David_Wheeler_(British_computer_scientist)) is quoted:

> All problems in computer science can be solved by another level of indirection, except for the problem of too many layers of indirection.

Abstraction are all around us and **without abstractions we'd be doomed.** After all, [if you wish to make an apple pie from scratch, you'd first have to invent the universe](https://en.wikiquote.org/wiki/Carl_Sagan). The key is understanding that no abstraction will ever be perfect and will leak in some ways. We should avoid the temptation of coding between the lines and fix the leaks instead.

Bad and over abstractions complicate things even more and should be avoided at all costs. In the next post, I'll talk about when its appropriate to build abstractions in a software project.
