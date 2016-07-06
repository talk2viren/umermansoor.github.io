---
layout: post
title: Blameless Postmortems - Examining Failure Without Blame
comments: True
excerpt_separator: <!--more-->
---

Let's face it: failure is inevitable in complex systems. It cares not for the number of tests you ran, code reviews or your monitoring tools. It just happens. And how is failure usually dealt with? Instead of learning from it to improve the resilience of the system, **the traditional view is to assign blame and point fingers at individuals responsible for the failure**. This happens because it's easier to identify the culprit than the actual cause. In [The Field Guide to Understanding Human Error](https://www.amazon.com/Field-Guide-Understanding-Human-Error/dp/0754648265), author [Sidney Dekker](http://sidneydekker.com/) refers to this as the "old view" that leads us nowhere:

<!--more-->

> When faced with a human error problem, you may be tempted to ask '**Why didn't they watch out better? How could they not have noticed?**'. You think you can solve your human error problem by telling people to be more careful, by reprimanding the miscreants, by issuing a new rule or procedure. These are all expressions of **'The Bad Apple Theory', where you believe your system is basically safe if it were not for those few unreliable people in it**. This old view of human error is increasingly outdated and will lead you nowhere. **The new view, in contrast, understands that a human error problem is actually an organizational problem**.

When employees are blamed and shamed by their superiors, who have the the power of hindsight on their side, few things happen:

- Employees become defensive and lose motivation. **The overall team sociology and culture suffers**.
- Employees start hiding mistakes. The team and the company doesn't learn any lessons and nothing is done to prevent failures from happening again.
- No one actually takes the responsibility and everybody blames each other.

So how should companies handle mistakes?

When failure occurs, the role of the management should be to figure out what happened so they can improve something to prevent it from happening again. **But the management doesn't have a crystal ball that can give out all the details**. They have to rely on their employees for this information. In order for employees to come forward and admit responsibility for their mistakes, the right type of culture and environment must be present. One that doesn't punish people for their mistakes. It requires a **"Just Culture"**. The CTO of Etsy, John Allspaw, [describes it](https://codeascraft.com/2012/05/22/blameless-postmortems/):

> Having a **Just Culture** means that you’re making effort to balance safety and accountability.  It means that **by investigating mistakes in a way that focuses on the situational aspects of a failure’s mechanism and the decision-making process of individuals** proximate to the failure, an organization can come out safer than it would normally be if it had simply punished the actors involved as a remediation.
>
**Having a “blameless” Post-Mortem process** means that engineers whose actions have contributed to an accident can give a detailed account of:
>
- what actions they took at what time,
- what effects they observed,
- expectations they had,
- assumptions they had made,
- and their understanding of timeline of events as they occurred.
>
…and that they can give this detailed **account without fear of punishment or retribution**.

For management, it starts with hiring good people and assuming that everyone involved in the failure had good intentions; they didn't think that the mistake was possible. If you have incompetent turkeys on your team, no amount of processes or metrics will save the project. After you have hired good people, trust them to make the right decisions. **Managers who constantly question the competence of their teams can't build productive teams**.

When failures occur, the goal should be to understand '*what*' caused the failure without focusing on the '*who*'. There are various techniques to get to the [root cause](https://en.wikipedia.org/wiki/Root_cause). I have been using a technique known as the '5 Whys' which works really well in most situations. It was developed by [Sakichi Toyoda](https://en.wikipedia.org/wiki/Sakichi_Toyoda) and its goal is to:

>  determine the root cause of a problem by **repeating the question "Why?" Each question forms the basis of the next question**.

Here's an example of 5 Whys in practice from [Joel's blog](http://www.joelonsoftware.com/items/2008/01/22.html):

> [Problem:] **Our link to Peer1 NY went down**
>
- *Why?* – Our switch appears to have put the port in a failed state
- *Why?* – After some discussion with the Peer1 NOC, we speculate that it was quite possibly caused by an Ethernet speed / duplex mismatch
- *Why?* – The switch interface was set to auto-negotiate instead of being manually configured
- *Why?* – We were fully aware of problems like this, and have been for many years.  But - we do not have a written standard and verification process for production switch configurations.
- *Why?* – Documentation is often thought of as an aid for when the sysadmin isn’t around or for other members of the operations team, whereas, it should really be thought of as a checklist.

For more comprehensive and formal analysis that goes beyond 'first stories', [John Allspaw](https://codeascraft.com/2012/05/22/blameless-postmortems/) collecting "second stories" from multiple sources and perspectives as part of the formal postmortem process:

> From [Behind Human Error](https://www.amazon.com/Behind-Human-Error-David-Woods/dp/0754678342) here’s the difference between “first” and “second” stories of human error:

| First Stories  | Second Stories  |
|---|---|
| Human error is seen as cause of failure	  |  Human error is seen as the effect of systemic vulnerabilities deeper inside the organization |
| Saying what people should have done is a satisfying way to describe failure  | Saying what people should have done doesn’t explain why it made sense for them to do what they did  |
| Telling people to be more careful will make the problem go away  | Only by constantly seeking out its vulnerabilities can organizations enhance safety  |

A *Just Culture and blameless postmortems* aren't about avoiding accountability. In fact they achieve the opposite effect by **creating a culture** where people can freely admit their mistakes and learn from them. **A mistake can be a great opportunity to learn a valuable lesson from**. When failure happens, a thorough analysis is performed, with an emphasis on **process over people, to make it better**. These analyses or 'postmortems' should be performed *after* the problem has been solved; when emotions aren't running high. The results or findings must be shared with the entire team or the company.

It'll be helpful to keep in mind that **most people have a natural tendency to assign blame to others**. Some do it more explicitly than the others. As managers when you are trying to create a *Just Culture*, you need to keep your eyes and ears open and when **you detect finger pointing, you must tactfully shift the focus away from people and back to the process**.
