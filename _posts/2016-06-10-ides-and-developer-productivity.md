---
layout: post
title: IDEs and Productivity
comments: True
excerpt_separator: <!--more-->
---

I used to be neutral on the choice and even the use of an IDE for writing code. When I was in the university, I learned and used [Vim](https://en.wikipedia.org/wiki/Vim_(text_editor)) to write code for my assignments. When I started my first job, I switched to a popular Java IDE ([NetBeans](https://netbeans.org/)) because everyone was using it and it had a nice debugger. Other than debugging and basic auto-completion, I didn’t learn the IDE much in terms of its features and used it like a text editor. But this changed recently. I ran into an issue compiling a Java project. I went over to the developer’s desk who had made the most recent commit. He opened up the project in his IDE and with **just a few keystrokes, he had the entire maven dependency graph displayed on his screen. It was magic**. Few more keystrokes and he was able to locate the root cause.

<!--more-->

It was at that point that I was convinced that **knowing how to effectively use an IDE has a profound impact on developer productivity**. This is especially true for complex enterprise applications, where superior knowledge of IDE can provide a significant boost in productivity.

I started out with Vim and loved it. When I first tried Eclipse/NetBeans for Java development, I was overwhelmed by the number things that were cluttered on the screen.

![Eclipse Clutter]({{ site.url }}/img/blogs/eclipse-clutter.jpg)

The second issue I had with Eclipse/NetBeans was that I had to use the mouse a lot for navigation. But I saw the its value in refactoring, autocomplete and generating boiler plate code so I switched. I wasn’t alone and there’s (still) a group of developers who don’t use an IDE, and instead use time-tested and powerful text editors like Vim or Emacs or newer editors like the [Sublime Text](https://www.sublimetext.com/). I worked with a developer who wrote code exclusively in Emacs. When I hired him, he told that he’ll use nothing but Emacs. I was sure that in time I’ll be able to ‘show him the light’ and he’ll see the value in using an IDE. But after watching him write code in Emacs, I realized that he’s obviously an expert and has more to lose than to gain by starting all over again in an IDE. I didn’t make any attempts to convince him to give up Emacs. On the contrary, he got me thinking that I should perhaps ditch Netbeans and switch to Emacs.

So if you are already using Vim or Emacs and feel comfortable using it, I won’t try to convince you to switch to an IDE. But if you sitting on the fence and don’t have a preference, **I’d recommend strongly recommend picking a good IDE and learning it really well**. The choice of the IDE will depend on the language are are programming in. I switched to **[IntelliJ IDEA](https://www.jetbrains.com/idea/) for Java and highly recommend it**. It’s extremely powerful and I feel like I can’t live without the *alt-enter* shortcut anymore. It [impressed Martin Fowler](http://martinfowler.com/bliki/PostIntelliJ.html) after developers in his company voluntarily switched to it:

> The biggest endorsement of IntelliJ came from ThoughtWorks developers. If anyone suggested a standard IDE for ThoughtWorks projects we needed tear-gas to control the riots. There were JBuilder zealots, textpad zealots, slickedit zealots - don't even get me started on the emacs zealots.
>
> Within six months nearly everyone was using IntelliJ. Voluntarily and eagerly.

But don't just use IntelliJ like any other IDE. **Make an effort to learn and understand its features to enhance the development process**. I’d also suggest learning keyboard shortcuts and **start relying on mouse less and less until you don't need to use it all when writing code**. I haven’t completely got there myself, but it is possible to do 100% mouse free development in IntelliJ. Watch this presentation by [Hadi Hariri](https://blog.jetbrains.com/idea/author/hhariri/) in which he demonstrates how to ditch the mouse and use IntelliJ only with the keyboard: [IntelliJ IDEA Tips and Tricks Full Version](https://www.youtube.com/watch?v=eq3KiAH4IBI).
