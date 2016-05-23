---
layout: post
title: Effective Coding Standards
comments: True
excerpt_separator: <!--more-->
---

Coding standards are a set of guidelines, best practices, programming styles and conventions that developers adhere to when writing source code for a project. All big software companies have them. Here are few guidelines from the ['Linux kernel coding style'](https://www.kernel.org/doc/Documentation/CodingStyle):

> **a.** Tabs are 8 characters, and thus indentations are also 8 characters.
>
> **b.** The limit on the length of lines is 80 columns and this is a strongly
preferred limit.
>
> **c.** The preferred form for allocating a zeroed array is the following:
>
```
p = kcalloc(n, sizeof(...), ...);
```
>
Both forms check for overflow on the allocation size n * sizeof(...),
and return NULL if that occurred.

<!--more-->

Recently, I came across a blog post from [Richard Rodger](https://twitter.com/rjrodger). In '[Why I Have Given Up on Coding Standards](http://www.richardrodger.com/2012/11/03/why-i-have-given-up-on-coding-standards/#.V0FHiJMrJsM)', he writes:

> Every developer knows you should have a one, exact, coding standard in your company. Every developer also knows you have to fight to get your rules into the company standard. Every developer secretly despairs when starting a new job, **afraid of the crazy coding standard some power-mad architect has dictated**.
>
> **It’s better to throw coding standards out and allow free expression**. The small win you get from increased conformity does not move the needle. Coding standards are technical ass-covering.

*Oh boy*. While I **disagree** with Richard that coding standards should be abandoned, I share his pain. I briefly worked with a nut job of a "senior developer" who came in as the lead for a project we'd been working on for 6 months. He was an academic who had just finished his PhD and had little experience working on real world projects. He spent first couple of weeks writing "coding standards" in *total isolation* like he was some kind of a God and we were lowly beings who just weren't good enough. His coding standards document was full of his personal opinions and promoted some insane form of coding style. The control freak *demanded* that we update the source code we had already written to reflect his standards. I have never witnessed team morale hit rock bottom so fast. Needless to say, I had a very brief stay at that job.

Another example that I can think of: a manager who insisted on being part of every major code review. During the reviews, he would flag formatting issues, that were almost always a matter of his own preference, as "errors". The worst part of the story is that *he hadn't written down his coding standards anywhere!* I guess he thought developers would learn his style through osmosis. Sometimes, it felt as if he made up rules on the fly. As I mentioned in my post on conducting [effective code reviews](http://codeahoy.com/2016/04/03/effective-code-reviews/), it is useless arguing over formatting issues if you don't have coding standards.

The point is that coding standards are often misunderstood by naive managers and control freaks who misuse them in one way or the other such that it achieves nothing (*no one follows them*) or causes friction within the team and hurts morale. Many software developers become bitter and start hating coding standards. Coding standards aren't the problem. Like any other tool, they become harmful when used incorrectly or in the wrong hands. Coding standards that suck have the following attributes:

- full of author's own opinions and personal coding style. Coding standards are not personal agendas.
- huge focus on style and formatting issues and is often vague.
- recommendations disguised as standards. I have made this [mistake](https://github.com/starscriber/coding-standards/wiki). **Standards must be treated like rules and hence must be enforceable**.

Good software developers and architects understand that coding style is very personal varies from individual to individual. They write coding standards that respect individual developers' freedom and allow them to express themselves. They do not attempt to mechanize the whole process, rather they focus on a few well-known practices that are widely accepted or plain common sense. And before any standard is put into practice, they get buy-in from the team, if the team wasn't already involved in formulating the standards. Here are **few examples of good coding standards** related to formatting:

- No more than one statement per line.
- Line length should not exceed 80 or 100 characters.
- Test class must start with the name of the class they are testing followed by 'Test'. E.g. `ServerConfigurationTest`.
- One character variable names should only be used in loops or for temporary variables.

All of these could be easily justified in a black-and-white manner without the enforcer appearing like a dictator. On the other extreme, here are some so called "standards" that will rub developers the wrong way and prompt un-necessary debates:

- Class names must not end in `-er`. *[Personal Opinion]*
- Don't use `static` fields or methods. *[Personal Opinion]*
- Aim for low Cohesion and High Coupling. *[Recommendation. Cannot be enforced.]*
- Use Test Driven Development. *[Recommendation. Cannot be enforced.]*

There is a grey area between common sense guidelines and personal preferences such as whether to put braces on the same line or the next. Standardize for the sake of consistency, but try to keep items in the grey area (generally formatting issues) to a bare minimum.

## Effective Coding Standards

Let's ask the question: why exactly do we need coding standards and what benefits do they offer? [Most](https://msdn.microsoft.com/en-us/library/aa291591(v=vs.71).aspx) [articles](https://www.sitepoint.com/coding-standards/) [I](https://www.smashingmagazine.com/2012/10/why-coding-style-matters/) found online draw a direct relationship between coding standards and software maintainability. While there is absolutely no doubt that source code that adheres to good standards is more readable and reflects harmony, there is another side of coding standards which is often overlooked at the expense of too much attention on aesthetics. **Effective coding standards focus on techniques that highlight problems and make bugs stand-out and visible to everyone**. [Joel said it better in 2005](http://www.joelonsoftware.com/articles/Wrong.html):

> Look for **coding conventions that make wrong code look wrong**.

In Java programming, having the following standards will help catch bugs early on and increase software quality:

- Whenever your override `equals()` method, you must also override the `hashCode()` method.
- Do not compare strings using `==` or `!=`.
- Do not ignore exceptions that you caught.
- Do not catch broad exception classes like `Exception` or `RuntimeException`.

To recap, effective coding standards:

1. are short, simple and concise rules. They *do not attempt to cover and processify everything* and leave plenty of room for developers to exercise their own creativity.
2. strike the right balance between formatting issues and issues that "*make the wrong code look wrong*".
3. are black and white instead of vague suggestions or recommendations.
4. Are adopted by the team and actively enforced. By having coding standards and not following them might [convey a sense of abandonment or that no one cares](http://codeahoy.com/2016/05/02/software-rot-entropy-and-the-broken-window-theory/).

If you want to look at an **example of good coding standards**, here are [Google's Coding Standards](http://google.github.io/styleguide/javaguide.html) for Java.

## Automate the Process of Checking Code Standards

Once you have effective coding standards, you should **automate the process** of checking source code's adherence to standards. It will save a lot of time in peer reviews and catch everything that humans might miss. There is an abundance of style checking tools available for all major [programming languages](https://en.wikipedia.org/wiki/List_of_tools_for_static_code_analysis). For our Java projects, we use a popular tool called [Checkstyle](http://checkstyle.sourceforge.net/). It provides several [Checks](http://checkstyle.sourceforge.net/checks.html) and by default, checks code against [Sun's conventions](http://www.oracle.com/technetwork/java/codeconvtoc-136057.html). But you could easily **choose [Google's coding standards](https://google.github.io/styleguide/javaguide.html) or customize to adopt to your own**. If you use IntelliJ IDEA (which I highly recommend), there's a [checkstyle plugin](https://plugins.jetbrains.com/plugin/1065) that shows results right inside the IDE.

![IntelliJ Checkstyle]({{ site.url }}/img/checkstyle-intellij.png)

In summary, coding standards, when used for the right reasons and in the right manner, offer many benefits. They make source code more readable and the software project more maintainable. They also help catch bugs and mistakes that are disguised as seemingly harmless code. While they might not catch all possible bugs, I'll take something over nothing any day.

## Suggested Reading
- [List of tools for static code analysis](https://en.wikipedia.org/wiki/List_of_tools_for_static_code_analysis)
- [Why Coding Standards Matter (blog)](http://paul-m-jones.com/archives/34)
- [FindBugs (Java)](http://findbugs.sourceforge.net/)
- [PMD](http://pmd.github.io/)
- [pep8 (python)](https://pypi.python.org/pypi/pep8)
