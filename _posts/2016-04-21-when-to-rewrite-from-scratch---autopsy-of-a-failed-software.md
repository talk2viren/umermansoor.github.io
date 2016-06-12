---
layout: post
title: When to Rewrite from Scratch - Autopsy of a Failed Software
comments: True
redirect_from: "/2016/04/21/rewrite-from-scratch-autopsy-of-a-failed-software-project/"
excerpt_separator: <!--more-->
---

It was winter of 2012. I was working as a software developer in a small team at a start-up. We had just released the first version of our software to a real corporate customer. The development finished right on schedule. When we launched, I was over the the moon and very proud. It was extremely satisfying to watch the system process couple of million of unique users a day and send out tens of millions of SMS messages. By summer, the company had real revenue. I got promoted to software manager. We hired new guys. The company was poised for growth. Life was great. **And then we made a huge blunder and decided to rewrite the software. From scratch.**

 <!--more-->

## Why We Felt That Rewrite from Scratch Was Needed?

We had written the original system with a gun to our heads. We had to race to the finish line. We weren't having long design discussions or review meetings - we didn't have time for such things. We would finish up a feature get it tested quickly and move on to the next. We had a [shared office](https://www.trtech.ca/) and I remember software developers at other companies getting into lengthy design and architecture debates and arguing for weeks over design patterns.

Despite agile-on-steroids design, the original system wasn't badly written and generally was well structured. There was some spaghetti code that carried over from company's previous proof of concept attempts that we left untouched because it was working and we had no time. But instead of thinking about incremental improvements, we *convinced* ourselves that we need to rewrite from scratch because:

- the old code was bad and hard to maintain.
- the "monolith java architecture" was inadequate for our future need of supporting a very large operator with 60 million mobile users and multi-site deployments.
- I *wanted* to try out new, shinny technologies like Apache Cassandra, Virtualization, Binary Protocols, Service Oriented Architecture, etc.

We convinced the entire organization and the board and sadly, we got our wish.

## The Rewrite Journey

The development officially began in spring of 2012 and we set end of January, 2013 as the release date. Because the vision was so grand, we needed even more people. I hired consultants and couple of remote developers in India. However, we didn't fully anticipate the need to maintain the original system in parallel with new development and underestimated customer demands. Remember I said in the beginning we had a real customer? The customer was one one of the biggest mobile operators in South America and once our system had adoption from its users, they started making demands for changes and new features. So we had to continue updating the original system, albeit half-heartedly because we were digging its grave. We dodged new feature requests from the customer as much as we can because we were going to throw the old one away anyways. This contributed to delays and we missed our January deadline. In fact, we missed it by 8 whole months!

But let's skip to the end. When the project was finally finished, it looked great and met all the requirements. Load tests showed that it can easily support over 100 million users. The configuration was centralized and it had a beautiful UI tool to look at charts and graphs. It was time to go and kill the old system and replace it with the new one... **until the customer said "no" to upgrade**. It turned out that the original system had gained wide adoption and their users had started relying on it. They wanted absolutely no risks. Long story short, after months of back and forth, we got nowhere. The project was officially doomed.

## Lessons Learnt

- You should almost never, ever rewrite from scratch. We rewrote for all the wrong reasons. While parts of code were bad, we could have easily fixed them with refactoring if we had taken time to read and understand the source code that was written by other people. We had genuine concerns about the scalability and performance of the architecture to support more sophisticated business logic, but we could have introduced these changes incrementally.
- Systems rewritten from scratch offer no new value to the user. To the engineering team, new technology and buzzwords may sound cool but they are **meaningless to customers** if they don't offer new features that the customers need.
- We **missed real opportunities** while we were focused on the rewrite. We had a very basic 'Web Tool' that the customer used to look at charts and reports. As they became more involved, they started asking for additional features such as real-time charts, access-levels, etc. Because we weren't interested in the old code and had no time anyways, we either rejected new requests or did a bad job. As a result, the customer stopped using the tool and insisted on reports by email. Another lost opportunity was an opportunity to build a robust Analytics platform that was *badly* needed.
- I underestimated the effort of maintaining the old system while the new one is in development. We estimated 3-5 requests a month and got 3 times as many.
- We thought our code was harder to read and maintain since we didn't use proper design patterns and practices that other developers spent days discussing. It turned out that most professional code I have seen in larger organizations is 2x time worst than that we had. So we were dead wrong about that.

## When Is Rewrite the Answer?

[Joel Spolsky made strong arguments against rewrite](http://www.joelonsoftware.com/articles/fog0000000069.html) and suggests that one should never do it. I'm not so sure about it. Sometimes incremental improvements and refactoring are very difficult and the only way to **understand** the code is to rewrite it. Plus software developers love to write code and create new things - it's boring to read someone else's code and try to understand their code and their 'mental abstractions'. But good programmers are also good maintainers.

If you want to rewrite, do it for the right reasons and plan properly for the following:

- The old code will still need to be maintained, in some cases, long after you release the new version. Maintaining two versions of code will require huge efforts and you need to ask yourself if you have enough time and resources to justify that based on the size of the project.
- Think about losing other opportunities and prioritize.
- Rewriting a big system is more risky than smaller ones. Ask yourself if you can incrementally rewrite. We switched to a new database, became a 'Service Oriented Architecture' and changed our protocols to binary, all at the same time. We could have introduced each of these changes incrementally.
- Consider the developers' bias. When developers want to learn a new technology or language, they want to write some code in it. While I'm not against it and it's a sign of a good environment and culture,  you should take this into consideration and weigh it against risks and opportunities.

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

Abandoning working projects is dangerous and we wasted an enormous amount of money and time duplicating working functionality we already had, rejected new features, irritated the customer and delayed ourselves by years. If you are embarking on a rewrite journey, all the power to you, but make sure you do it for the right reasons, understand the risks and plan for it.
