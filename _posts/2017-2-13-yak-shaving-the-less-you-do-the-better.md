---
layout: post
title: Yak Shaving - the Less You Do the Better
comments: True
excerpt_separator: <!--more-->
---

The term **yak shaving** was [coined](http://projects.csail.mit.edu/gsb/old-archive/gsb-archive/gsb2000-02-11.html) at MIT back in the 90's.

> Yak shaving is what you are doing when you're doing some stupid, fiddly little task that bears no obvious relationship to what you're supposed to be working on, but yet a chain of twelve causal relations links what you're doing to the original meta-task.

I asked a QA tester to test a simple HTTP/REST application. A task I thought shouldn't take more than a few days. A few days later:

>Me: "How is the testing going?"
>
QA: "Going alright."
>
Me: "Any luck catching them bugs yet?"
>
QA: "Haven't started writing tests yet."
>
Me: "*Huh?*"
>
QA: "I was setting up the testing environment in all its glory. Once it is done, I will update the test client to dynamically fetch the server-side configuration to the tests are run with the right expectations... Didn't you see my Scrum board this morning? All of the yaks I need to shave listed under the parent story."

<!--more-->

I looked at the Scrum board and he wasn't kidding. His entire sprint was dedicate to yak shaving and following best QA practices and processes. The actual task of writing test wasn't scheduled for another couple of sprints. He wasn't doing anything wrong. He was just following the organizational policies.

One thing I have always loved about startups is their ability to ship things very quickly, without being sidetracked. I have seen a couple of fresh graduates at a startup finish the system on time compared to a larger team of experienced developers at a corporation struggling to finish a similar system. Why? Because **[startups and solo developers have fewer yaks to shave](http://sethgodin.typepad.com/seths_blog/2005/03/dont_shave_that.html)**. They don't have time to follow shinny processes and practices like planning and retrospect meetings, continuous releases, centralized configuration, certification processes. These things almost always manifest as more hairy yaks that require shaving. Arguably, this is not scalable - but it gets the job done.

![replacing-a-lightbulb]({{ site.url }}/img/replacing-a-lightbulb-imgur.gif)

I'm not suggesting that yak shaving in evil: sometimes, you have no choice but to go on side quests before you can reach your final destination. Developers spend a vast majority of their time shaving yaks. Ben Ramsey said it better: yak shaving "**[isn't just part of our jobs, it's the entire job description.](https://benramsey.com/blog/2015/11/yak-shaving/)**" So the minute you start going down the yak shaving path, stop and ask yourself if shaving the yak is really necessary. **The fewer yaks you have to shave, the faster you'll get to your destination**.
