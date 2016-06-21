---
layout: post
title: Blameless Postmortems - Examining Failure Without Blame
comments: True
excerpt_separator: <!--more-->
---

Let's face it: **mistakes are inevitable in creative processes that require brain power**. Software design and development falls into this category. When people are building complex systems, things go wrong. Even with detailed designs and processes, there are hundreds of micro-decisions that are made by the developers when they are writing code. But the **traditional view is to assign blame and point fingers when something fails.** In [The Field Guide to Understanding Human Error](https://www.amazon.com/Field-Guide-Understanding-Human-Error/dp/0754648265), author [Sidney Dekker](http://sidneydekker.com/) refers to this as the "old view" that leads nowhere:

<!--more-->

> When faced with a human error problem, you may be tempted to ask '**Why didn't they watch out better? How could they not have noticed?**'. You think you can solve your human error problem by telling people to be more careful, by reprimanding the miscreants, by issuing a new rule or procedure. These are all expressions of **'The Bad Apple Theory', where you believe your system is basically safe if it were not for those few unreliable people in it**. This old view of human error is increasingly outdated and will lead you nowhere. **The new view, in contrast, understands that a human error problem is actually an organizational problem**.

When employees are blamed by their superiors who have the power of hindsight on their side, they become defensive. When this happens, **the team sociology suffers and employees lose motivation to do good work**. The "new view" assumes that everyone involved in the failure had good intentions and they didn't think the mistake was possible. It starts with hiring good people. If you have incompetent turkeys on your team, no amount of processes or metrics will save the project. After you have hired good people, trust them to make the right decisions. **Managers who constantly question the competence of their teams can't build productive teams**.

When failure occurs, the goal should be to understand '*what*' caused the failure without focusing on the '*who*'. **Mistakes should be analyzed with a perspective of learning**. John Allspaw, the CTO of Etsy, calls this type of the analysis the "[blameless postmortems](https://codeascraft.com/2012/05/22/blameless-postmortems/)":

> [...] **by investigating mistakes in a way that focuses on the situational aspects of a failure’s mechanism and the decision-making process of individuals** proximate to the failure, an organization can come out safer than it would normally be if it had simply punished the actors involved as a remediation.
>
Having a “blameless” Post-Mortem process means that engineers whose actions have contributed to an accident can give a detailed account of:
>
- what actions they took at what time,
- what effects they observed,
- expectations they had,
- assumptions they had made,
- and their understanding of timeline of events as they occurred.
>
…and that they can give this detailed **account without fear of punishment or retribution**.

When employees are punished or shamed for their mistakes, they would become hesitant of giving out actual details of what happened. This simply guarantees that **the mistake will be repeated again in the future because nothing was done to fix the [root cause](https://en.wikipedia.org/wiki/Root_cause)**. There are various techniques to get to the root cause. I have been using a technique known as the '5 Whys' which works really well in most situations. It was developed by [Sakichi Toyoda](https://en.wikipedia.org/wiki/Sakichi_Toyoda) and its goal is to:

>  determine the root cause of a problem by **repeating the question "Why?" Each question forms the basis of the next question**.

Here's an example of 5 Whys in practice from [Joel's blog](http://www.joelonsoftware.com/items/2008/01/22.html):

> [Problem:] **Our link to Peer1 NY went down**
>
- *Why?* – Our switch appears to have put the port in a failed state
- *Why?* – After some discussion with the Peer1 NOC, we speculate that it was quite possibly caused by an Ethernet speed / duplex mismatch
- *Why?* – The switch interface was set to auto-negotiate instead of being manually configured
- *Why?* – We were fully aware of problems like this, and have been for many years.  But - we do not have a written standard and verification process for production switch configurations.
- *Why?* – Documentation is often thought of as an aid for when the sysadmin isn’t around or for other members of the operations team, whereas, it should really be thought of as a checklist.

For more comprehensive analysis that goes beyond 'first stories', [John](https://codeascraft.com/2012/05/22/blameless-postmortems/) suggests looking for "second stories" that are collected from multiple sources and perspectives:

> From [Behind Human Error](https://www.amazon.com/Behind-Human-Error-David-Woods/dp/0754678342) here’s the difference between “first” and “second” stories of human error:

| First Stories  | Second Stories  |
|---|---|
| Human error is seen as cause of failure	  |  Human error is seen as the effect of systemic vulnerabilities deeper inside the organization |
| Saying what people should have done is a satisfying way to describe failure  | Saying what people should have done doesn’t explain why it made sense for them to do what they did  |
| Telling people to be more careful will make the problem go away  | Only by constantly seeking out its vulnerabilities can organizations enhance safety  |

*Blameless postmortems* create a culture where people are not afraid of admitting their mistakes, in fact, they happily provide details so others in the team can avoid the same mistake in the future. **Mistakes are viewed as an opportunity to learn valuable lessons from**. When failure happens, a thorough analysis is performed, with an emphasis on **process over people, to make sure it doesn't happen again**. These postmortems should be performed after the problem has been solved when emotions aren't running high. The results or findings must be shared with the entire team or the company.

One thing I have experienced is that **some people have a natural tendency to assign blame to others**. Some do it more explicitly than the others. As managers, you need to keep your eyes and ears open and when **you detect finger pointing, you must tactfully shift the focus away from people and back to the process** - the '*what*' and the '*how*', not the '*who*'.