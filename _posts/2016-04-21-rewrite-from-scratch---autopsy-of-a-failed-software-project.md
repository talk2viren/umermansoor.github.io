---
layout: post
title: Rewrite from Scratch - Autopsy of a Failed Software Project
comments: True
excerpt_separator: <!--more-->
---

It was winter of 2012. I was working in a very small team for a start-up. We had just released the first version of our software and had a real corporate customer. We finished development right on schedule - Launch was a little delayed because of a few contractual hiccups. But we launched. And I was over the the moon. It was extremely satisfying to watch the system process couple of million of unique users a day and send out tens of millions of SMS messages.

I was very proud of everything we had achieved. By summer, the company had real revenue. I got promoted from Software Developer to Software Manager. We hired new guys. Life was great. The company was poised for growth. **And then we made a huge blunder and decided to rewrite the software. From scratch.**

 <!--more-->

## Why We Felt That Rewrite from Scratch Was Needed?

We had written the original system with a gun to our heads. We had to race to the finish line. We weren't having long design discussions or review meetings - we didn't have time for such things. We would finish up a feature get it tested quickly and move on to the next. We had a [shared office](https://www.trtech.ca/) and I remember software developers at other companies getting into lengthy design and architecture debates and arguing for weeks over which design pattern is better suited for their problem.

Despite agile-on-steroids design, the original system wasn't badly written. It did have some spaghetti code that we didn't touch because it was working and we had no time.

The next prospective customer in the pipeline was one of the largest mobile operators in India with over 60 Million users. And we needed scalability to support multi-site deployment. We convinced ourselves that we need to rewrite from scratch because:

- the old code was bad and hard to maintain.
- the "monolith architecture" was inadequate for our future needs.
- I wanted to try out new, shinny technologies like Apache Cassandra, Virtualization, Binary Protocols, Service Oriented Architecture, etc.

We convinced the entire organization and the board and sadly, we got our wish.

## The Rewrite Journey

The development officially began in Summer of 2012 and we set end of January, 2013 as the release date. Because the vision was so grand, we needed even more people. I hired consultants and couple of remote developers in India. However, we didn't fully anticipate the need to maintain the original system in parallel with new development and underestimated customer demands. Remember I said in the beginning we had a real customer? The customer was one one of the biggest mobile operators in South America and once our system had adoption from its users, they started making demands for changes and new features. So we had to continue updating the original system, albeit half-heartedly because we were digging its grave, while working on the new one in parallel. We dodged new feature requests from the customer as much as we can because we were going to throw the old one away anyways. This contributed to delays and we missed our January deadline. In fact, we missed it by 8 whole months!

But let's skip to the end. When the project was finally finished, it looked great. Load tests showed that it can easily support over 100 Million active users with more than ideal latency and minimum hardware. The configuration was centralized and it had a beautiful UI tool to look at charts and graphs. It was time to go and kill the old system and replace it with the new one... **until the customer said "No" to upgrade**. It turned out that the original system had gained wide adoption and their users had started relying on it. They wanted absolutely no risks. Long story short, after months of back and forth, operational and deployment issues, we abandoned the new system and the project was officially doomed.

## Lessons Learnt

- You should almost never, ever rewrite from scratch. We rewrote because the old project had [spaghetti code](https://sourcemaking.com/antipatterns/spaghetti-code) but it was a small portion of code and we could have had easily refactored. While we had genuine concerns about the scalability and performance of the architecture to support more sophisticated events, we could have done a better job if we had updated and refactored things incrementally.
- Systems rewritten from scratch offer no new value to the user. To the engineering team, new technology and buzzwords may sound cool but they are **meaningless to customers** if they don't offer new features that the customers need.
- We **missed real opportunities** while we were focused on the rewrite. We had a very basic Web Tool that the customer used to look at charts and reports. As they became more involved, they started asking for additional features such as real-time charts, access-levels, etc. Because we weren't interested in the old code and had no time anyways, we either rejected new requests and did a bad job. As a result, the customer stopped using the tool and insisted on reports by email. Another lost opportunity was an opportunity to build a robust Analytics platform that was *badly* needed.
- I underestimated the effort of maintaining the old system while the new one is in development. We estimated 3-5 requests a month and got 3 times as many.
- We thought our code was harder to read and maintain since we didn't use proper design patterns and practices that other developers spent days discussing. It turned out that most professional code I have seen in larger organizations is 2x time worst than that we had. So we were dead wrong about that.

## When Is Rewrite the Answer?

Almost never, but in some cases, incremental improvements and refactoring is very difficult. If the code you are working on is truly a genuine mess, I guess the short answer would be that you **can rewrite** if you have a lot of great developers and you understand and prepare for the fact that the **old code will still need to be maintained, in some cases, even after you release the new version**.

Michael Meadows made [excellent observations](http://programmers.stackexchange.com/questions/6268/when-is-a-big-rewrite-the-answer) on when "BIG" rewrite becomes necessary:

> **Technical**
>
- The coupling of components is so high that changes to a single component cannot be isolated from other components. A redesign of a single component results in a cascade of changes not only to adjacent components, but indirectly to all components.
- The technology stack is so complicated that future state design necessitates multiple infrastructure changes. This would be necessary in a complete rewrite as well, but if it's required in an incremental redesign, then you lose that advantage.
- Redesigning a component results in a complete rewrite of that component anyway, because the existing design is so fubar that there's nothing worth saving. Again, you lose the advantage if this is the case.
>
> **Political**
>
- The sponsors cannot be made to understand that an incremental redesign requires a long-term commitment to the project. Inevitably, most organizations lose the appetite for the continuing budget drain that an incremental redesign creates. This loss of appetite is inevitable for a rewrite as well, but the sponsors will be more inclined to continue, because they don't want to be split between a partially complete new system and a partially obsolete old system.
- The users of the system are too attached with their "current screens." If this is the case, you won't have the license to improve a vital part of the system (the front-end). A redesign lets you circumvent this problem, since they're starting with something new. They'll still insist on getting "the same screens," but you have a little more ammunition to push back.
Keep in mind that the total cost of redesigning incrementally is always higher than doing a complete rewrite, but the impact to the organization is usually smaller. In my opinion, if you can justify a rewrite, and you have superstar developers, then do it.

## Summary

In this post, I looked back at what I consider one of the biggest mistakes of my career: rewriting a working system from scratch. Abandoning working projects is dangerous and we wasted an enormous amount of money and time duplicating working functionality we already had, rejected new features, irritated the customer and delayed ourselves by years. 
