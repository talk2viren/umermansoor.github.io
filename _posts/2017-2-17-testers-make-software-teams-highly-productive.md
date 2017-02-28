---
layout: post
title: Testers Make Software Teams Highly Productive
comments: True
excerpt_separator: <!--more-->
---

To put it mildly, developers are not great at testing their own products. Bias, pride, wrong assumptions, lack of time, switching contexts, all play a role in making developers ineffective at testing their code. Most companies, especially startups, don't fully understand the role of a tester. Very early on in my career, we made the **mistake of hiring people to be testers who applied for a software developer position but weren't good enough programmers**. We paid for it in terms of software quality and over-worked team. It wasn't until I worked with some great testers that I realized how effective and productive software teams become when they have great testers on board.

<!--more-->

The best testers or quality assurance engineers I've ever worked with weren't developers. One was a DevOps guy and the other was a Network Engineer. They became testers coincidentally because they were very smart and tremendously effective at finding bugs, even when they tested the system as a black-box. They treated the whole exercise like puzzle solving. Here's an example bug report from them:

> "Found an issue with API to retrieve all widgets right after registering as a new user. Requests kept timing out. Upon digging further, I discovered that the system is throwing NullPointerExceptions. This is happens because the *xyz* counter in the database wasn't properly initialized in the previous step. Initialize the counter in the previous step and also catch all un-handled exceptions and return an error to the user."

Or this one.

> "Found an issue. The system throws exception when the user selects option 5. I checked the logs and found nothing. I ran wireshark to look at the request and response and found the issue to be caused by developers sending a unicode character in the request. Don't send unicode characters and log error reasons which is field_10 in the XML payload."

These bug reports not only clearly identified the issue, but also the root cause and suggested a better course of action, thus refining the product and improving its quality.

Some organizations **naively assume that automated tests written by developers can replace testers**. Wrong, very wrong. I love automated tests. But a good tester finds deficiencies and suggests improvements that a developer or an automated suite may overlook:

> *Tester*: When I pass a string instead of a number for the billing amount, the app doesn't capture it. You need to check the type and ensure its a number.
>
> *Developer*: What do you mean? I wrote the test for it just last week and my end-to-end client did in fact receive the status code 4000 which means it's an error.
>
> *Tester*: Yes, the error is returned. But the error comes from the database in the form of an exception when it tries to store a string where a number should go. This is inefficient because the database call is expensive.
>
*Developer*: Yeah. That makes sense when I think a about it. I will get it fixed.

Another great reason for having dedicated testers on the team is to provide **[positive reinforcement](https://www.joelonsoftware.com/2010/01/26/why-testers/) and closure to developers** who may otherwise be doubtful whether they are on the right track or not. Developers usually breathe a sigh of relief when their releases are certified by testers and are told that everything works as expected.

> A great tester gives programmers immediate feedback on what they did right and what they did wrong. Believe it or not, one of the most valuable features of a tester is providing positive reinforcement. There is no better way to **improve a programmer’s morale, happiness, and subjective sense of well-being than ~~a La Marzocco Linea espresso machine~~ to have dedicated testers who get frequent releases from the developers, try them out, and give negative and positive feedback**.

Until next time.
