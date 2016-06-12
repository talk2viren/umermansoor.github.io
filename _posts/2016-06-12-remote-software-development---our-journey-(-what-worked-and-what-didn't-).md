---
layout: post
title: Remote Software Development - Our Journey (What Worked and What Didn't)
comments: True
excerpt_separator: <!--more-->
---

In my [previous post](http://www.codeahoy.com/2016/04/21/when-to-rewrite-from-scratch-autopsy-of-a-failed-software/), I talked about the ill-fated rewrite of our flagship product. The '[second system](https://en.wikipedia.org/wiki/Second-system_effect)' although better and faster than its predecessor was rejected by the customer and wasn't deployed anywhere (with the exception of a few pilot projects). But the story has a silver lining - one thing that we got right in the process that allowed my previous employer to pivot and release two more products commercially. **We were able to assemble a highly productive remote development team**. In this post I'll talk about our journey and summarize my recommendations (observations) in the end.

 <!--more-->

I wrote the original system with one of the best [duct-tape developers](http://www.joelonsoftware.com/items/2009/09/23.html) I know. I called him '*The Code Machine*'. He didn't write clean code, but he wrote it fast as hell and made sure it worked. The original system, done with a gun to our heads, was a big, complex, monolith application and the **boundaries between its modules were ill defined**. Computer scientists refer to this type of code as [tightly-coupled](https://en.wikipedia.org/wiki/Coupling_(computer_programming)) and the system is referred to as [non-orthogonal](https://en.wikipedia.org/wiki/Orthogonality_(programming)).

The 'second system' had a more grand vision and we needed to grow the team to build it right and on schedule. I hired another developer, a bright, young CS graduate to work on the system. One problem with the original system quickly became evident to me: because the code was tightly-coupled (the auto-generated UML class diagrams resembled a spider's cobweb), the new developer couldn't work independently. While they were not bickering, the two developers couldn't get out of each other's way. Any **feature that was assigned to the new developer overlapped with *the code machine's* work and the progress slowed down**. Parallel to all this, the C-suite of my company decided to try out 'offshore software development'. The Chairman of the Board recommended a company with office in Hyderabad, India and before I even knew it, I was asked to interview Java developers from a list of CVs.

The coupling problem was magnified by the arrival of the remote developer. First, because the system was so complex, I had to spend a **tremendous amount of time and energy helping him drive up the learning curve**. Finally, when he was able to implement new features, his changes conflicted with the work of the local team and **integration became a nightmare which required everyone's involvement**. This issue aside, I had other concerns as well. It appeared that he was 'covering his ass' by leaving a paper trail. I hate bureaucracy and don't work that way. I later learned, through other means, that developers in that offshore firm were paid below the market rate and the culture was just terrible. No wonder they had huge turn-over issue.

Let me stop here to summarize the situation: we had a **complex system** with huge overlap of responsibilities. Changes rippled through the whole system and affected the entire team. We had a **remote developer in India**, outside of our comfort (time)zone, and integration often forced everyone to work odd hours. To make matters worse, the remote developer had a **low morale** and was interested in covering his ass. And I don't say to demean him: he was a reasonably good developer but the environment he was working in was toxic.

It was then that I decided to slow ourselves down and think about how we are going to do this. Since we didn't have a dedicated Q/A, I assigned that responsibility temporarily to the new developer so he could be productive (it was dick move, but he was allowed time to learn and practice his coding skills and he ended up becoming a [SCJP](http://education.oracle.com/pls/web_prod-plq-dad/db_pages.getpage?page_id=320) during that period). The remote developer got re-assigned to a new R&D project that the CEO was cooking up.

When the things calmed down, we realized that three things must happen if we want to scale our development:

1. The new system must be orthogonal. It should be modular and modules should communicate with other only using well defined messages (i.e. through the public API). I wanted the modules to be 100% blackbox... [abstractions that do not leak](http://codeahoy.com/2016/05/06/good-abstractions-have-fewer-leaks/).
2. Each module must be assigned to a single developer or a team who will 100% own it. I wanted to get around the [Brook's Law](https://en.wikipedia.org/wiki/Brooks%E2%80%99_law) by minimizing communication between local and remote developers as much as possible.
3. We must do something about the culture and fix the working conditions for our remote developers. We needed the right people.

## 1. Building Orthogonal System

In two-dimensional geometry, two lines are orthogonal if they intersect each other at right angles. This affords a kind of **independence**, where you could move along one of the lines without affecting your position projected onto the other line. This simply means that in an orthogonal system, you could easily change the 'networking layer' without affecting the 'database layer' or the 'UI layer'. In [Pragmatic Programmer](https://www.amazon.com/Pragmatic-Programmer-Journeyman-Master/dp/020161622X), Andrew and David used an excellent analogy to describe a non-orthogonal system:

> You're on a helicopter tour of the Grand Canyon when the pilot, who made the
obvious mistake of eating fish for lunch, suddenly groans and faints. Fortunately,
he left you hovering 100 feet above the ground. You rationalize that the collective
pitch lever controls overall lift, so lowering it slightly will start a gentle descent
to the ground. However, when you try it, you discover that life isn't that simple.
The helicopter's nose drops, and you start to spiral down to the left. Suddenly you
discover that you're flying a system where every control input has secondary
effects. Lower the left-hand lever and you need to add compensating backward
movement to the right-hand stick and push the right pedal. But then each of
these changes affects all of the other controls again. Suddenly you're juggling an
unbelievably complex system, where every change impacts all the other inputs.
Your workload is phenomenal: your hands and feet are constantly moving, trying
to balance all the interacting forces.
>
Helicopter controls are decidedly not orthogonal.

We took the layered approach to system design similar to the [OSI model](https://en.wikipedia.org/wiki/OSI_model). The layers we built had following qualities:

- Each layer had a single major responsibility.
- Layers exposed a well-defined API. Communication with other layers was done only using the API.
- Layers (with the exception of business logic) communicated with only one other layer.

Here's what the design of the design looked like:

![orthogonal system]({{ site.url }}/img/blogs/orthogonal_system.png)

In addition to the layers, there were several smaller subsystems not shown in the diagram above such as the 'Admin UI' tool and simulators for testing the system end-to-end.

## 2. Circumventing Brook's Law

Brook's Law is a bitch. It basically says that the communication overhead increases as the number of people involved in a project increases. Communication with the remote team that is outside +/- 5 hours of your timezone becomes a burden. We had a full 12 hours time difference. So we decided to extend the principal of single responsibility. Each layer, module or sub-system shall have a single owner. Initially, I was little hesitant of creating silos that might affect team work, but we had a great team and the ownership never became an issue. The upper management was concerned about '**an important team member getting hit by a bus**' and wanted some level of duplicity. To achieve that, we decided that we'll keep the system as simple as possible, perform code reviews and document it well. Our approach to documentation was simple:

- For every layer or module, I came up with a **high-level product document**. The goal was to describe the *what and why* (not how) in simple and concise terms. The document had **a vision that described what the module would do in one sentence and why we were building it, a 10,000 foot overview, core requirements and a list of the things that module shall not do**.
- The developers created design documents. They were a brief description of how the project was organized into sub-packages, followed by [interaction diagrams](https://en.wikipedia.org/wiki/Interaction_overview_diagram). On average, this document was mostly 3-6 diagrams with very little text.

The documentation was kept in the GitHub wiki along with the source code.

So we had divided the system in a manner that individual pieces could be assigned independently to remote teams and the instructions were clear enough to minimize communication. We wanted to assign ownership and get out of their way. But there was a big problem: our remote developer wasn't motivated enough to take ownership. Growing that team was out of the question since we had no control over the environment or the culture.

## 3. Getting the Right People On the Bus

As the remote developer was finally starting to become productive, I had concerns about his morale due to absurd working conditions and bureaucracy in the company that was outsourcing his services. I reached the conclusion that the things cannot be improved and we terminated the contract.

I was so disheartened by the state of remote software development that I was ready to write it off, but a fellow CTO for a different company told me that **they're getting great results on oDesk**. I tried to experiment a little and asked for $1500/month budget that was approved. I contracted with an independent consultant on a part-time basis to work on our website that I had created in HTML and PHP. **The guy was a winner**. Whenever I assigned him a new task, he would keep asking question until he fully understood it. He would then go away and send me regular updates on the progress. His releases were on time and his code was pristine. He didn't program in Java, but had (equally smart) friends who did. It's a long story but he helped us grow the team and we hired four full time developers who setup their own office to work from.

I visited the remote team once or twice a year. These visits helped me understand the issues these guys faced and the face-to-face whiteboard meetings enabled them to understand more complex sub-systems easily.

## Summary

I purposely told the story of how my company built a remote software development team instead of giving recommendations so you guys could draw your own conclusions. In retrospect, we went through trial and error and luckily found the right people that made it work. But despite all of that, it was a strenuous journey. **If the remote developers require constant babysitting and you cannot measure their productivity, it's probably not working**. If you could afford it, **steer clear of remote developers working +/- 3 outside of your time zone**. If you decide to take the offshore route, here are the key things you must remember:

- **Divide the system into independent layers and minimize the overlap between the layers**. Most layers should talk to one, may be two other layers.
- Go to extreme lengths to hire the right people; avoid offshore sweatshops that have developers with low morale and high turnover. It will take time and effort to make remote developers productive and turnover will hurt twice as bad. **Treat them with respect as you would afford to a local developer, make them feel like they are part of the family, and make sure they are being compensated fairly**. Don't make them stay up late nights to attend meetings. Instead of making five guys stay up late to talk to me, I would schedule the weekly meeting during my evening, their morning.
- Provide clear requirements. I cannot stress this enough. Be as concise and as crystal clear as possible. If the requirements aren't clear, there will be a lot of back and forth. Don't take requirements written by The Suits and send them straight to the developers. Take time to digest them, **turn them into black and white requirements with test criterion before engaging developers**. It sounds like a lot of work, and it is, but it's much better than being on late-night calls and wasting man-days of work because a remote developer is blocked and is waiting for answers.
- Be fair to the local team and minimize the meetings to once a week with few exceptions here and there.
