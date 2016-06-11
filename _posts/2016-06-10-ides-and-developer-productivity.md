---
layout: post
title: IDEs and Productivity
comments: True
excerpt_separator: <!--more-->
---

I used to be neutral on the choice and even the use of an [IDE](https://en.wikipedia.org/wiki/Integrated_development_environment) for writing code. When I was in university, I learned and used [Vim](https://en.wikipedia.org/wiki/Vim_(text_editor)) for assignments. When I started my first job, I switched to a [popular Java IDE](https://netbeans.org/) because everyone at work was using it and it featured a nice debugger. Other than debugging and basic auto-completion, I didn’t learn the IDE much in terms of its features. But that changed one day when I ran into an issue compiling a Java project. I went over to the developer’s desk who had made the most recent commit. He opened up the project in his IDE and in **just a few keystrokes, he had the entire [maven dependency graph](https://blog.jetbrains.com/idea/2010/05/maven-dependencies-diagram/) displayed on his screen. Just like magic**. Few more keystrokes and he was able to locate the root cause. I was **surprised** because after months of using the IDE, I had no idea that it had that feature. Two things happened:

<!--more-->

- I realized that up until that point, I was using the IDE like a text editor and missing out on many of its powerful features.
- I became convinced that **knowing how to effectively use an IDE has a profound impact on developer productivity**. This is especially true for complex enterprise applications, where superior knowledge of IDE can provide a significant boost in productivity.

I started out with Vim and loved it. When I first tried Eclipse/NetBeans for Java development, I was overwhelmed by the number things that were **cluttered** on the screen.

![Eclipse Clutter]({{ site.url }}/img/blogs/eclipse-clutter.jpg)

The second thing that put me off was that I had to use the mouse a lot for navigation. But I saw the value in refactoring, code-completion and generating boiler-plate code so I decided to bite the bullet and switched to it from Vim. In retrospect, it was a good decision.

Many developers who become proficient in text editors like Vim or [Emacs](https://en.wikipedia.org/wiki/Emacs) don't switch to an IDE. I worked with a developer who wrote code exclusively in Emacs. When I hired him, he told that he’ll use nothing but Emacs. I was sure that in time I’ll be able to ‘show him the light’ and that he’ll see the value in using an IDE. But after watching him write code in Emacs, I realized that he was obviously an expert in Emacs and **had more to lose than gain by starting all over again in an IDE**. I didn’t make any attempts to convince him to give up Emacs for an IDE. (On the contrary, he got me thinking that I should perhaps ditch my IDE and switch to Emacs.)

So if you are already using Vim or Emacs and are productive in it, I won’t try to convince you to switch to an IDE. But if you sitting on the fence and don’t have a preference, **I’d strongly recommend picking up a good IDE and learning it really well**. The choice of the IDE will depend on the language you are are programming in. I have been using **[IntelliJ IDEA](https://www.jetbrains.com/idea/) for Java development and highly recommend it**. It’s extremely powerful and I feel like I can’t live without the *alt-enter* shortcut anymore. It [impressed Martin Fowler](http://martinfowler.com/bliki/PostIntelliJ.html) after developers in his company voluntarily switched to it:

> The biggest endorsement of IntelliJ came from ThoughtWorks developers. **If anyone suggested a standard IDE for ThoughtWorks projects we needed tear-gas to control the riots**. There were JBuilder zealots, textpad zealots, slickedit zealots - don't even get me started on the emacs zealots.
>
> **Within six months nearly everyone was using IntelliJ. Voluntarily and eagerly**.

But don't just use IntelliJ like any other IDE. **Make an effort to learn and understand its features to enhance the development process**. I’d also suggest learning keyboard shortcuts and **start relying on the mouse less and less until you don't need to use it all when writing code**. I haven’t completely got there myself, but it is possible to do 100% mouse free development in IntelliJ. Watch this presentation by [Hadi Hariri](https://blog.jetbrains.com/idea/author/hhariri/) in which he demonstrates how to ditch the mouse and use IntelliJ only with the keyboard: [IntelliJ IDEA Tips and Tricks Full Version](https://www.youtube.com/watch?v=eq3KiAH4IBI).

## Notes:
- [Enjoying Java and Being More Productive with IntelliJ IDEA](https://blog.jetbrains.com/idea/2016/03/enjoying-java-and-being-more-productive-with-intellij-idea/)
- For Python development, I use [PyCharm](https://www.jetbrains.com/pycharm/) which is developed by the same guys as IntelliJ IDEA.
- I replaced Sublime Text with [Atom](https://atom.io/) by GitHub on my machines.
- Mark Seemann makes some strong points [against the use of productivity tools](http://blog.ploeh.dk/2013/02/04/BewareofProductivityTools/) (including IDEs). You be the judge.
