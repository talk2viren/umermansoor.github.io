---
layout: post
title: Software Estimates are not Targets
comments: True
excerpt_separator: <!--more-->
---

Software estimation is a hard problem. So much so that in 2012, when [Woody Zuill](https://twitter.com/WoodyZuill) tweeted his [blog post](http://zuill.us/WoodyZuill/2012/12/10/no-estimate-programming-series-intro-post/) with the hashtag [#NoEstimates](https://twitter.com/hashtag/noestimates?src=rela), he set the software development community on fire. Everyone from discontented developers to seasoned software veterans flocked to the discussion on Twitter on either side of the debate. (You can read more about the #NoEstimates movement [here](http://ronjeffries.com/xprog/articles/the-noestimates-movement/).) In this post, let's try to answer the question whether we need estimates and then look at what software estimates are and more importantly what they aren't.

[Tom Demarco](https://en.wikipedia.org/wiki/Tom_DeMarco) gave the following tongue-in-cheek definition of a software estimate:

> “An estimate is the most optimistic prediction that has a non-zero probability of coming true.”

Which brings me to my next point.

 <!--more-->

## Do We Need Estimates?
The short answer is *yes, we do*.

We need estimates for the following reasons:

- To know when something will be done.
- To provide a release date.
- To allocate cost, resources and people.
- To make go/no-go calls: pursue the development or kill the proposal.

**We need estimates to make decisions** and **estimates form the foundation of plans**. Good estimates allow us to make sound decisions and to chart a course of action.

## What is an Estimate?

Google gives the following [definition](https://www.google.com/webhp?sourceid=chrome-instant&ion=1&espv=2&ie=UTF-8#q=define+estimate) of an estimate:

> 1. roughly calculate or judge the value, number, quantity, or extent of.
> 2. an approximate calculation or judgment of the value, number, quantity, or extent of something.

Strictly speaking, the dictionary's definition is correct but the term has very different connotations in the software world. Estimates are frequently **confused with organizational targets and plans. An organizational target is a desirable outcome that benefits the organization in one way or the other. It is an objective that the organization seeks**. When you hear statements that sound similar to the following, immediately know that the speaker is talking about the organization's targets, not estimates:

- *"The goal is to have the new feature released by May 19th all users."*
- *"We must have the product ready by the end of this month for our show and tell."*
- *"We need this feature released in time for Christmas."*
- *"We must get the module updated ASAP to align with the new regulation."*

While estimates are related to targets, the two are are entirely different beasts. **A target is a goal which requires a sound plan to achieve it. In turn plans require estimates. But estimation doesn't need to look at a target or a plan. Estimates should be completely unbiased**. If it requires 6 weeks to update a module to use DynamoDB instead of Cassandra, it will take 6 weeks irrespective of the target which could be 4 weeks or 6 months. Good plans incorporate estimates to figure out how to meet the target. If the target is to have the DynamoDB upgrade complete in 4.5 weeks instead of 6 weeks, the plan might include additional resources to speed up the process.

> An estimate is NOT a target. **Estimates** are unbiased; **Plans** are heavily biased and rely on estimates to figure out how to meet the **target**.

I wish I had known the difference early on in my career. Many times I got requests along the following lines:

> "*Umer*, we need this product ready in 2 months or we'll lose the bid to the other vendor." - Account Manager

I used to get all worked up at what I felt were unrealistic deadlines and estimates. In hindsight, these non-technical managers were just sharing business commitments and targets. And there's nothing wrong with it. *(In some cases though, I'd have appreciated if they had consulted the team before making any commitments but that's another story.)* Without knowing the difference between estimates and plans, I committed to the imposed deadlines. Even if the product was done on time, there were negative side-effects. The team was overworked, the work was of low-quality and [technical debt was accrued](http://codeahoy.com/2016/04/27/do-not-let-technical-debt-get-out-of-control/). I accepted targets as estimates and as a substitute for a plan. In his book [Software Estimation: Demystifying the Black Art](http://www.amazon.com/Software-Estimation-Demystifying-Developer-Practices/dp/0735605351), which is a definitive work on the subject, Steve McConnel provides a more productive way to deal with such requests:

> EXECUTIVE: How long do you think this project will take? We need to have this
software ready in 3 months for a trade show. I can’t give you any more team
members, so you’ll have to do the work with your current staff. Here’s a list of
the features we’ll need.
>
PROJECT LEAD: Let me make sure I understand what you’re asking for. Is it more
important for us to deliver 100% of these features, or is it more important to
have something ready for the trade show?
>
EXECUTIVE: We have to have something ready for the trade show. We’d like to
have 100% of those features if possible.
>
PROJECT LEAD: I want to be sure I follow through on your priorities as best I can.
If it turns out that we can’t deliver 100% of the features by the trade show,
should we be ready to ship what we’ve got at trade show time, or should we plan
to slip the ship date beyond the trade show?
>
EXECUTIVE: We have to have something for the trade show, so if push comes to
shove, we have to ship something, even if it isn’t 100% of what we want.
>
PROJECT LEAD: *OK, I’ll come up with a plan for delivering as many features as
we can in the next 3 months*.

(In this example, the actual estimate to finish the project was 5 months.)

Steve gives the following definition of a "Good Estimate" in his book:

> A good estimate is an estimate that provides a clear enough view of the project reality to allow the project leadership to make good decisions about how to control the project to hit its targets.

100% agreed. The key take aways from this post are:

- Estimates are a crucial part of any plan, and a bad plan is better than no plan at all.
- Estimates ≠ Targets.
- Estimates are unbiased.
- Plans are heavily biased and incorporate estimates to meet targets.

Software estimation is tough. There are many unknowns and uncertainties. It's like driving on the [Bay Bridge](https://en.wikipedia.org/wiki/San_Francisco%E2%80%93Oakland_Bay_Bridge) to get to San Francisco in dense fog and broken headlights, only to receive a phone call from the hosts that the party has moved to the opposite side. Software estimation is tough, but it's certainly not magic. This post touched upon the definition of software estimates and the relationship between estimates, plans and targets. Later posts will talk about how to get better at estimating. In the meantime, go buy a copy of [Steve's book](http://www.amazon.com/Software-Estimation-Demystifying-Developer-Practices/dp/0735605351). You will not regret it.
