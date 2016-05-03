---
layout: post
title: Do Not Let Technical Debt Get Out of Control
comments: True
excerpt_separator: <!--more-->
---


Technical debt is a useful metaphor for describing the consequences of adding new functionality to a system in a **quick and dirty** manner to get something out of the door faster. The proper way would have resulted in a much cleaner design and implementation, but would also have taken much longer. [Martin Fowler](http://martinfowler.com/bliki/TechnicalDebt.html) calls technical debt a wonderful metaphor:

> Technical Debt is a **wonderful metaphor** developed by Ward Cunningham to help us think about this problem. In this metaphor, doing things the quick and dirty way sets us up with a technical debt, which is similar to a financial debt. Like a financial debt, the technical debt incurs interest payments, which come in the form of the **extra effort that we have to do in future development because of the quick and dirty design choice**.

Taking on technical debt should be a **strategic decision** where all stakeholders must understand the consequences and risks involved. Like most financial debts, it should not be taken recklessly and interest **payments must be paid on time to avoid penalties**.

 <!--more-->

While technical debt has negative connotations, it is an **unavoidable reality** for many software projects. In her [book](http://www.amazon.com/Practical-Object-Oriented-Design-Ruby-Addison-Wesley/dp/0321721330) on Practical Object-oriented Design in Ruby: An Agile Primer, [Sandi Metz](http://www.sandimetz.com/) wrote:

> Sometimes the value of having the feature right now is so great that it outweighs any future increase in costs. If lack of a feature will force you out of business today it doesn’t matter how much it will cost to deal with the code tomorrow; you must do the best you can in the time you have.

At Starscriber, we accrued technical debt from time to time to take advantage of new business opportunities and tried to pay it off as soon as the dust settled. But we didn’t always succeed. There were at least two projects where the debt got out of control. Implementing (or hacking, would be a better word) new features was a complex and **painful process** for everyone involved: developers, testers and operations teams. The change requests didn't stopped coming and we made the mistake of **not acknowledging that we are accumulating way too much technical debt and letting it drag on for way too long**. The result? It became a huge burden and required a lot of effort just to keep the system running.

However, in my opinion the biggest casualty wasn’t the productivity; it was the team culture and the morale. **People got demotivated since they took no pride in their work**. They couldn’t: no creativity or learning was involved, other than finding creative ways to ‘hack’ and make it work. And since the project required **tribal knowledge** to understand all the hacks, we had to keep the 'demotivated' team together until we could take it no more and had to **halt new development to do major refactoring and testing**. Some teams consider [rewrite from scratch when facing this predicament, which is almost always a big mistake](http://codeahoy.com/2016/04/21/when-to-rewrite-from-scratch-autopsy-of-a-failed-software/).

The lessons to be learned are:

### 1. Acknowledge
Technical debt is inevitable and will become a major problem if ignored. We got sidetracked in the pursuit of pleasing a big client and didn't acknowledge or realize that we are accruing big time technical debt which later caused bugs and slowed down our ability to add new features.

### 2. Decide Strategically: Understand Short-Term vs Long-Term Consequences
In your career, it will often make sense to ship a subpar system to gain market advantage. In fact, it would be a mistake not to. In our last [startup](http://www.paperistic.com/), we made the opposite mistake and didn’t deliver a *minimum viable product* on time. As a result, we lost out on crucial early feedback. The **trick is that all stakeholders must understand and strategically prioritize** the tradeoffs of speed vs quality. Non-technical stakeholders often don’t understand the concept and [Steve McConnell](http://www.ontechnicaldebt.com/blog/steve-mcconnell-on-categorizing-managing-technical-debt/) has some good advice on how to educate them:

> **Technical staff should build on the technical debt metaphor as a way to talk to business staff to explain that if they take on short-term technical debt, they will need to pay it off or else it will end up costing the business on the long-term**. For example: “If we spend X weeks working on this particular infrastructure area, it will allow us to add features A, B, and C. Although the work itself does not show immediate benefit, it will open the door for other work that will produce business benefits later on.”  Now this becomes a productive discussion and we have a reason for the business to engage.

### 3. Don't Let it Get out of Control: Pay the Debt
The longer you wait, the higher interest payments get. If you let technical debt accrue and not pay if off, future development will stall, code quality will suffer, tribal knowledge will be required to understand all the hacks and people working on the project will become demotivated. Have a plan for paying off the technical debt to avoid massive interest payments in the future. It should be a part of your normal development process.

Taking on technical debt is risky business: it gives you the short-term benefits, but you'll have to pay the debt back with interest in the future. Interests will keep on accruing and the more you delay paying the debt off, the higher the interest payments are going to be. Dealing with technical debt is not always easy: **upper management often don't see the value** since it doesn't result in new features; **Things break and you might even have to throw some code out**. Embark on the journey and have faith that you are doing the right thing and that your system will be in a better shape than it was before.

In the [next post or so](http://codeahoy.com/2016/05/02/software-rot-entropy-and-the-broken-window-theory/), we’ll look at Software Entropy. Please like and follow us on [Facebook](https://www.facebook.com/codeahoy) and [Twitter](http://twitter.com/codeahoy) to stay up-to-date.
