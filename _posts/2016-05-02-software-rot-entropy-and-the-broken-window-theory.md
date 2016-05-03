---
layout: post
title: Software Rot, Entropy and the Broken Window Theory
comments: True
excerpt_separator: <!--more-->
---

![Broken Windows]({{ site.url }}/img/broken_windows.jpg)

> “Complexity is the business we are in and complexity is what limits us.” - Fred Brooks, The Mythical Man-Month

Software projects go through many modifications over their lifetime. As they evolve, the code grows in size and complexity creeps in. Software developers spend a large portion of their time **maintaining** existing software either by adding new functionality or fixing bugs. Often times, they are forced to take **shortcuts** to meet deadlines. Developers add new functionality in a 'quick and dirty' manner and apply duct-tape to defects. While the organization meets its short-term goal of getting the software out of the door quickly, the code quality suffers and deteriorates. After a while, things start to get really bad. The software becomes so complex and buggy, that it is virtually impossible to maintain. Fixing a bug would introduces more bugs and modifying one part of the software would break several others.

<!--more-->

Let's look at a related concept called **software entropy**. Entropy is the amount of **disorder** in a system. It is a physical phenomenon but [Ivar Jacobson et al](http://www.amazon.com/Object-Oriented-Software-Engineering-Approach/dp/0201544350) used it to describe the disorder in a software system:

> The second law of thermodynamics, in principle, states that a closed system's disorder cannot be reduced, it can only remain unchanged or increased. A measure of this disorder is entropy. This law also seems plausible for software systems; **as a system is modified, its disorder, or [software] entropy, always increases**. This is called Software Entropy.

When the 'disorder' or the *software entropy* increases, it leads to **[software rot](https://en.wikipedia.org/wiki/Software_rot)**. When the software or code rot gets bad, people become frustrated and consider major refactoring or in some cases [rewriting from scratch](http://codeahoy.com/2016/04/21/when-to-rewrite-from-scratch-autopsy-of-a-failed-software/). These solutions will fix the problem in the short-term but the software will rot again if the team doesn't adopt a plan for keeping future complexity under control.

While there are many factors that lead to software rot, the most important ones, according to [Andrew Hunt](https://en.wikipedia.org/wiki/Andy_Hunt_(author)) and [Dave Thomas](https://en.wikipedia.org/wiki/Dave_Thomas_(programmer)), are the **psychology and the team culture**. In their book, [The Pragmatic Programmer](http://www.amazon.com/Pragmatic-Programmer-Journeyman-Master/dp/020161622X), they argue that **software entropy is contagious and if not controlled, becomes an epidemic**.

> In inner cities, some buildings are beautiful and clean, while others are rotting hulks. Why? Researchers in the field of crime and urban decay discovered a fascinating trigger mechanism, one that very quickly turns a clean, intact, inhabited building into a smashed and abandoned derelict .
>
A broken window.
>
> **One broken window, left unrepaired for any substantial length of time, instills in the inhabitants of the building a sense of abandonment — a sense that the powers that be don’t care about the building. So another window gets broken**. People start littering. Graffiti appears. Serious structural damage begins. In a relatively short space of time, the building becomes damaged beyond the owner’s desire to fix it, and the sense of abandonment becomes reality.

Broken window theory was proposed by criminologists [James Wilson](https://en.wikipedia.org/wiki/James_Q._Wilson) and [George Kelling](https://en.wikipedia.org/wiki/George_L._Kelling) and had an enormous impact on police policy throughout the 1990s. While the broken window theory was been [widely](http://www.smithsonianmag.com/smart-news/sorry-malcolm-gladwell-nycs-drop-in-crime-not-due-to-broken-window-theory-12636297/?no-ist) [criticized](http://chronicle.uchicago.edu/060330/brokenwindow.shtml), I think it makes sense. When shortcuts are taken poor design decisions are made, it sends a signal that no one cares or that no one is in charge. Since its a feature of the environment, even good software developers *might* fall for it. The solution according to Andrew and Dave is simple:

> **Don’t leave “broken windows”** (bad designs, wrong decisions, or poor code) unrepaired. Fix each one as soon as it is discovered. If there is insufficient time to fix it properly, then board it up. **Perhaps you can comment out the offending code, or display a “Not Implemented” message, or substitute dummy data instead. Take some action to prevent further damage and to show that you’re on top of the situation**.

A simple comment around ugly code stating that its ugly and needs to get fixed soon is better than nothing. Code can quickly rot once windows start breaking. Even a mere perception of disorder could result in total chaos. Don't leave broken windows, fix them as soon as you could. Entropy will creep in - don't let it win.

