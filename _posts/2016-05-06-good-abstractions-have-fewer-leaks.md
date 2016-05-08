---
layout: post
title: Good Abstractions Have Fewer Leaks
comments: True
excerpt_separator: <!--more-->
---

> Abstraction is one of the greatest visionary tools ever invented by human beings to imagine, decipher, and depict the world. - Jerry Saltz

Abstraction are all around us. We abstract things to hide details making it easier to see the "big picture" and help cope with the complexity. When I push down on the gas pedal, a lot of magic happens under the hood to move my car. But I don't have to know any of that. I only ever need to know that pushing on the pedal increases the speed and releasing it will decrease it. The gas pedal is a **simple interface and that's what makes it so great**. It has been with us for a very long time and it's here to [stay](https://forums.teslamotors.com/forum/forums/what-term-should-we-use-gas-pedal).

<!--more-->

In software, like any other engineering discipline, abstractions are everywhere: the protocols, the frameworks, the libraries, the game engines, the file systems, even the programming languages we use everyday are abstractions of [low-level languages](https://en.wikipedia.org/wiki/Low-level_programming_language). It can be argued that everything in computer science is inevitably an abstraction of something more complicated under the hood. The higher-level abstractions provide useful functionality in the form of a black-box, **hiding all the complexity and implementation details**.

Except that it's not always so black and white. **Software abstractions are never perfect** and, arguably, are far from perfect. Abstractions are deficient when you have to **understand the low-level details or peek under the covers to understand what's going**. Consider Java's garbage collection process. It would have been great if I never had to think about how it works - after all, it was supposed to work like magic. But in reality, when a bug was reported that **"requests are taking a lot longer to process"**, I had to go in and figure out that the garbage collection was slowing things down. One cannot optimize performance and avoid penalties without learning how the garbage collector works (at least not for high-throughput, low-latency applications). Joel Spolsky called this the [law of leaky abstractions](http://www.joelonsoftware.com/articles/LeakyAbstractions.html):

>  All non-trivial abstractions, to some degree, are leaky.
>
> Abstractions fail. Sometimes a little, sometimes a lot. There's leakage. Things go wrong. It happens all over the place when you have abstractions.

![Leaky Pipe]({{ site.url }}/img/leaky_abstraction.jpg)

Joel is right. All abstractions in software are leaky: they attempt to hide away the complexity but the underlying details are not complete hidden. However, **the truth is that we can't live without abstractions.** Without abstractions and modules, I can't even begin to comprehend how any large software project could be maintained. Without Java's garbage collection, my code will be cluttered with `delete` statements and memory leaks will be all over the place. The flexibility offered by the the garbage collector works 90% of the time. It leaks because the underlying function of automatic memory management is very complex and highly irregular to which there isn't a general purpose solution. To its credit, garbage collection provides ways to tweak performance depending on the specific needs.

I don't think Joel meant that we should abandon abstractions altogether. I guess the point was to embrace the fact that all abstractions are leaky. **Edge cases will often require developers to get their hands dirty by understanding the framework to optimize performance or to troubleshoot. And that's perfectly fine.** Good abstractions and frameworks reduce the need for someone to understand the inner workings. Bad abstractions leak a lot and expose their users to all the details. When I heard of Remote Procedure Call (RPC), I loved the concept. It'd allow me to call a method on a remote server as easily as I would a local method. Great. There was a learning curve with [Apache Thrift](https://thrift.apache.org/) and it was well worth it. The framework, [albeit leaky](http://www.valuedlessons.com/2008/06/my-experience-with-message-passing.html), saved me from writing my own client/server and dealing with the communication complexity (although it was later replaced with REST). Similarly, compared to C++ string library which Joel cites as being leaky, strings in Java are lot less leaky (probably because they are a native feature of the language.)

Leaky abstraction aside, over the years, I have seen both good and terrible abstractions masqueraded as APIs. Not in distant memory, I had to fix an "abstraction" **that failed to hide the functionality** it provided and had very tight coupling with the applications that used it. 30% of the module's logic was scattered outside of it, weaved throughout other applications using java annotations. Another time I was reviewing some code and came across some very complex logic. It turned out that the developer that created an "abstraction" over Hibernate to provide an even more general and completely useless solution. Hibernate is already an abstraction over SQL! **Adding another layer on top of Hibernate made things even more complex than they needed to be**. As [David J. Wheeler](https://en.wikipedia.org/wiki/David_Wheeler_(British_computer_scientist)) is quoted:

> All problems in computer science can be solved by another level of indirection, except for the problem of too many layers of indirection.

Abstraction are all around us and **without abstractions we'd be doomed.** After all, [if you wish to make an apple pie from scratch, you'd first have to invent the universe](https://en.wikiquote.org/wiki/Carl_Sagan). Abstractions provide us the flexibility to work with something very complicated. Imagine the complexity of dealing with blobs of bits and bytes on magnetic platters, or on semiconductor chips, instead of files and databases! Understanding what goes on under the hood from time to time is a good trade-off considering that most of the time, the flexibility offered by a good abstraction would be sufficient.

As software developers, our goal should be to build less leaky abstractions. I also mentioned a few examples of bad and over abstractions that complicate things even more and should be avoided at all costs. In the next post or so, I'll talk about modular software and how abstractions help software development and when they become a pain.
