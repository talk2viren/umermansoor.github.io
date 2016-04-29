---
layout: post
title: Do Not Let Technical Debt Get Out of Control
comments: True
excerpt_separator: <!--more-->
---

Technical debt is an excellent metaphor that describes the consequences of adding new functionality to a system in a ‘quick and dirty’ manner to get some “urgent” functionality out of the door as soon as possible. The alternative approach would have resulted in a much cleaner design and implementation, but would also have taken much longer.  [Martin Fowler](http://martinfowler.com/bliki/TechnicalDebt.html) describes the metaphor:

> In this metaphor, doing things the quick and dirty way sets us up with a technical debt, which is similar to a financial debt. Like a financial debt, the technical debt incurs interest payments, which come in the form of the **extra effort that we have to do in future development because of the quick and dirty design choice**.

 <!--more-->

While technical debt has negative connotations, the truth is that it is an **unavoidable reality** for any software project. At Starscriber, we accrued technical debt from time to time to take advantage of new business opportunities and tried to pay it off (refactoring and testing) as soon as the dust settled. We didn’t always succeed. There were at least two projects where the debt got out of control. Implementing (hacking, would be a better word) new features was a complex and painful process for everyone involved: developers, testers and operations teams.  The change requests never stopped coming and we made a mistake of **not acknowledging that we are accumulating way too much technical debt and letting it drag on for way too long**. The result? It became a huge burden and required a lot of effort, just to keep the system running.

However, to me the biggest casualty wasn’t the productivity; it was the team culture and the morale. **People got demotivated since they took no pride in their work**. They couldn’t: no creativity or learning was involved, other than finding creative ways to ‘hack’ and make it work. And since the project required **tribal knowledge** to understand all the hacks, we had to keep the 'demotivated' team until we found a solution.

The lessons to be learned are:

### 1. Acknowledge Technical Debt
It is an unavoidable reality and could become a major problem if ignored.

### 2. Understand the Short-term vs Long-term Consequences
It will often make sense to ship a subpar system to gain market advantage. In fact, it would be a mistake not to. In our last [startup](http://www.paperistic.com/), we made the opposite mistake and didn’t deliver a *minimum viable product* on time and as a result, lost out on crucial early feedback. The trick is for all stakeholders to understand and prioritize based on the tradeoffs of speed vs quality. Non-technical stakeholders often don’t understand the concept and it is your job to educate them. [Steve McConnell](http://www.ontechnicaldebt.com/blog/steve-mcconnell-on-categorizing-managing-technical-debt/) has some good advice:

> Technical staff should build on the technical debt metaphor as a way to talk to business staff to explain that if they take on short-term technical debt, they will need to pay it off or else it will end up costing the business on the long-term. For example: “If we spend X weeks working on this particular infrastructure area, it will allow us to add features A, B, and C. Although the work itself does not show immediate benefit, it will open the door for other work that will produce business benefits later on.”  Now this becomes a productive discussion and we have a reason for the business to engage.

### 3. Do not let technical debt get out of control
If you let technical debt accrue and not pay if off, future development will be crippled, code quality will suffer, tribal knowledge will be required to understand all the hacks and people working on the project will become demotivated. Paying off technical debt should be a part of your normal development process to avoid massive interest in the future.

In the next post, we’ll look at Software Entropy and whether the ‘disorder in a software system’ is a bad thing.
