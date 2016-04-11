---
layout: post
title: Effective Code Reviews
comments: True
excerpt_separator: <!--more-->
---

Code review is the process in which code written by a developer is inspected by another person to look for defects and improvements. In other words, developers work on their code mostly in isolation and call for a review when they are ready.

Code reviews are a proven way to increase software quality. At Google, all code is [peer reviewed](http://goodmath.scientopia.org/2011/07/06/things-everyone-should-do-code-review/). To quote a few examples from [Code Complete 2](http://www.amazon.com/Code-Complete-Developer-Best-Practices-ebook/dp/B00JDMPOSY):

> * IBM’s 500,000 line Orbit project used 11 levels of inspections. It was delivered early and had only about 1 percent of the errors that would normally be expected.
> * A study of an organization at AT&T with more than 200 people reported a 14 percent increase in productivity and a 90 percent decrease in defects after the organization introduced reviews.
> * Jet Propulsion Laboratories estimates that it saves about $25,000 per inspection by finding and fixing defects at an early stage.

However, many teams still struggle with effective code reviews and don’t reap full benefits. In dysfunctional teams and organizations, it can quickly turn into a **nasty experience** for everyone involved:

* It becomes a platform for reviewers show-off their skills by pointing “mistakes” in someone else’s code and imposing their own “opinions” that have absolutely no merit.
* Developers become so defensive that they don’t want their code reviewed until it is absolutely ready – arguably  this could be a good thing, but misses the point of code reviews.
* Developers abandon code ownership and start relying on others to find issues.

In this post, I’ll talk about few things teams and organizations could do to make code reviews a pleasant experience for everyone involved.
<!--more-->
## Advice to Management: Create the Right Culture

Effective code reviews require a healthy culture that values quality and excellence. Code reviews will not give you the desired results if the team doesn’t believe in delivering high-quality products. You need a positive culture in which people are engaged – one that thrives on constructive criticism and allows the best ideas to win.

Other than creating a great culture and allowing time and resources to conduct reviews, management role in reviews should be kept to a minimum. It’s cultural and most people don’t want to air their dirty laundry in front of their superiors. Code reviews are best conducted by peers and management should never ask for details it could use to judge people – yes, some managers require check-lists and grades to be produced so they can “measure” and judge people.

May be you already have a great culture (count yourself lucky). May be you are half-way there. Creating the right culture depends on many factors (both internal to the team and organizational). It can be extremely challenging and there is no magic formula. Without the right culture, code reviews will not bring desired gains or at extremes, could become counter-productive.

## Everyone: Remember the Human

In his book, [Peer Reviews in Software: A Practical Guide](http://www.amazon.com/Peer-Reviews-Software-Practical-Guide/dp/0201734850), Wiegers writes:

> The dynamics between the work product’s author and its reviewers are critical. The author must trust and respect the reviewers enough to be receptive to their comments. Similarly, the reviewers must show respect for the author’s talent and hard work. Reviewers should thoughtfully select the words they use to raise an issue, focusing on what they observed about the product. Saying, “I didn’t see where these variables were initialized” is likely to elicit a constructive response, whereas “You didn’t initialize these variables” might get the author’s hackles up.

It is easy to become fixated on the code, but remember, there’s a human at the other end of the table (or computer). A human who has opinions. A human who is entitled to have an ‘ego’. Remember that there are many ways to solve a problem.

* Be humble. I have seen both highly productive reviews and very unproductive ones because someone decided to be prick – don’t be a prick:-)
* Make sure you have coding standards in place. Coding standards are shared set of guidelines in an organization with buy-in from everyone. If you don’t have coding standards, then don’t let the discussion turn into a pissing contest over coding styles (opening braces ‘{‘ on the same line or the next!) If you run into a situation like that, take the discussion offline to your coding standards forum.
* Learn to communicate well. You must be able to clearly express your ideas and reasons.
* Programming decisions are a matter of opinion. Reviewers and developers should seek to understand each other’s perspective but shouldn’t get into a philosophical debate.

## Advice to Reviewers: Be Humble

* The developer isn’t there to be sitting duck. Remember the purpose is to not demonstrate who the better programmer is: it is finding defects and to ensuring that the code is simple and maintainable.
* Ask questions. Don’t make demands or statements which could sound accusatory. For example, don’t say: “You didn’t follow standard XYZ”. A better way would be to genuinely seek to understand developer’s perspective: “What do you think about Standard XYZ and if it’s applies here?”, which brings me to my next point,
* Avoid “why did you“, “why did you not” style questions. It could put people on the defensive. “Why did you make this a global variable?” could be better expressed as “I don’t understand why this is a global variable “.
Look for ways to simplify the code. One of the goals of code reviews is to create ‘maintainable’ software.
* Remember to appreciate and thank the other person. People often forget how far a simple “great job” or “it looks great” could go.

Some of these things won’t work if they come off as rehearsed or said in a sarcastic tone. Treat the code review as you would a normal conversation. You are listening to another person and should genuinely seek to understand their perspective. Offer suggestions and tips when they are necessary. If the code is great, don’t be compelled to find something negative to say about it.

## Advice to Developers: It is not Personal

* Don’t take things personally. Remember that the villains are the defects or inadequacies in the code, not you.
* Recognize that you may be attached your code and that it is normal. If you take pride in your work, that’s a good sign that you are someone who cares about the craft.
* Have just the right amount of ego – enough to trust and defend your ideas but not so much that good suggestions and ideas are rejected without merit.
* To err is human. The reviewer is acting as second set of eyes and could point things that you might have overlooked. Questions are as valuable as concrete advice.
* Ask specific questions. “Does it make more sense to move all these classes into their own package?”
* Thank the reviewer for their time and any feedback they might have provided.

## Summary

Peer reviews are about people interacting with one another and ineffectiveness stems from sociological issues. Yet managers spend a lot of time worrying about which shiny tools to use – while tools will help, they alone won’t magically bring the results. Start with the right culture and… remember the human:-)

<p class="message">
I would love to hear your feedback, comments, thoughts on conducting effective code reviews. Please leave a
comment below.  
</p>
