---
layout: post
title: Effective Coding Standards
comments: True
excerpt_separator: <!--more-->
---

Coding standards are a set of guidelines, best practices, programming styles and conventions that developers adhere to when writing source code for a project. To understand, let's take a look at few coding standards from the ['Linux kernel coding style'](https://www.kernel.org/doc/Documentation/CodingStyle):

> **a.** Tabs are 8 characters, and thus indentations are also 8 characters.
There are heretic movements that try to make indentations 4 (or even 2!)
characters deep, and that is akin to trying to define the value of PI to
be 3.
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

*Oh boy*. While I **disagree** with Richard that coding standards should be abandoned, I can see where he's coming from. I briefly worked with a nut-job of a "senior developer" who came in as the project lead that we had worked on for almost 6 months. He was an academic who had just finished his PhD and had little experience working as a software developer. He spent first 2 weeks writing "coding standards" in total isolation like he was some kind of a God and we were lowly beings who just weren't good enough. His coding standard documentation was full of his personal opinions and his insane coding style. The control freak demanded that we update the source code we had already written to adopt his standards. No explanation given. I have never seen team morale go down so fast and needless to say, I had a very brief stay at that job.

Another example I can think of is a manager who insisted on being part of every major code review. During the reviews, he would point out so-called 'errors and 'deviations' from his "coding standards" that were purely formatting issues. The irony: he hadn't written down his coding standards anywhere! I guess he thought developers would learn his style through osmosis. Sometimes, we felt as if he made up rules on the fly. As I mentioned in my post on conducting [effective code reviews](http://codeahoy.com/2016/04/03/effective-code-reviews/): it is useless to argue over formatting issues if you don't have coding standards.

The point is that coding standards are often misunderstood by naive managers and control freaks who misuse it in one way or the other such that it achieves nothing (*no one follows them*) or causes friction within the team. **The problem isn't with coding standards. It is how they are used. They can be a wonderful practice that offers tremendous benefits especially for a large software project**.

Coding standards that suck have the following attributes:

- full of author's own opinions and personal coding style.
- huge focus on style and formatting.
- recommendations disguised as standards. **Standards must be treated like rules and hence must be enforceable**.

Good software developers and architects understand that coding style is very personal varies from individual to individual and they write coding standards that respect their freedom and allows them to express themselves. They do not mechanize the whole process rather they focus on a few well-known practices that are widely accepted or common sense items. And before any standard is put into practice, they get buy-in from the team, if the team wasn't already involved in formulating the standards. Here are **few examples of good coding standards** related to formatting:

- No more than one statement per line.
- Line length should not exceed 80 or 100 characters.
- Test class must start with the name of the class they are testing followed by 'Test'. E.g. `ServerConfigurationTest`.
- One character variable names should only be used in loops or for temporary variables.

All of these could be easily justified in a black-and-white manner without the enforcer appearing like a dictator. On the other extreme, here are some so called "standards" that will rub developers the wrong way and prompt un-necessary debates"

- Class names must not end in `-er`. *[Personal Opinion]*
- Don't use `static` fields or methods. *[Personal Opinion]*
- Aim for low Cohesion and High Coupling. *[Recommendation. Cannot be enforced.]*
- Use Test Driven Development. *[Recommendation. Cannot be enforced.]*

There is a grey area between common sense and personal preferences such as whether to put braces on the same line or the next. Standardize for the sake of consistency, but try to keep items in the grey area (generally formatting issues) to a minimum.

## Effective Coding Standards

Let's ask the question: why exactly do we need coding standards and what benefits do they offer? [Most](https://msdn.microsoft.com/en-us/library/aa291591(v=vs.71).aspx) [articles](https://www.sitepoint.com/coding-standards/) [I](https://www.smashingmagazine.com/2012/10/why-coding-style-matters/) found online draw a direct relationship between coding standards and software maintainability. There is no doubt that source code that adheres to a standard is consistent and easier to comprehend. But effective coding standards offer another huge benefit which is often overlooked at the expense of focusing on aesthetics. **Effective coding standards focus on techniques that highlight problems and make bugs stand-out and visible to everyone**. [Joel said it better in 2005](http://www.joelonsoftware.com/articles/Wrong.html):

> Look for **coding conventions that make wrong code look wrong**.

In Java programming, the following standards will help catch bugs early on and increase software quality:

- Whenever your override `equals()` method, you must also override the `hashCode()` method.
- Do not compare strings using `==` or `!=`
- Do not ignore exceptions that you caught.
- Do not catch broad exception classes like `Exception` or `RuntimeException`.

To recap, effective coding standards:

1. are simple. They do not attempt to process-ify everything and allow plenty of room to developers to exercise their creativity.
2. strike the right balance between formatting issues and issues that "make wrong code look wrong".
3. are black and white rules instead of vague suggestions or recommendations.
4. Are adopted by the team and actively enforced.

If you want to look at an **example of good coding standards, here are [Google's Coding Standards](http://google.github.io/styleguide/javaguide.html)** for Java.

## Automating Coding Standards

I mentioned that good coding standards must be enforceable. Once you have enforceable standards, **automate the process of checking whether or not the code adheres to standards**. It will save a lot of time in peer reviews and catch everything that humans might miss. There are several style checking tools available for [several programming languages](https://en.wikipedia.org/wiki/List_of_tools_for_static_code_analysis). For our Java projects, we use a popular tool called [Checkstyle](http://checkstyle.sourceforge.net/). It provides several [Checks](http://checkstyle.sourceforge.net/checks.html) and by default, checks code against [Sun's code conventions](http://www.oracle.com/technetwork/java/codeconvtoc-136057.html). But you could as easily **choose [Google's Coding Standards](https://google.github.io/styleguide/javaguide.html) or customize to adopt to your own**. If you use IntelliJ Idea (which I highly recommend), there's a [checkstyle plugin](https://plugins.jetbrains.com/plugin/1065) that shows results right inside the IDE:

![IntelliJ Checkstyle]({{ site.url }}/img/checkstyle-intellij.png)

**Effective coding standards** guarantee that the source code is more readable and more maintainable. Peer reviews can be conducted more efficiently as reviewers have no trouble understanding the code that adheres to well-known company standards. It helps new developers drive up the learning curve faster. Effective coding standards also highlight subtle mistakes and detect bad patterns that might cause errors. They might not catch all possible bugs, but it is sure as hell better than nothing.

## Suggested Reading
- [List of tools for static code analysis](https://en.wikipedia.org/wiki/List_of_tools_for_static_code_analysis)
- [Why Coding Standards Matter (blog)](http://paul-m-jones.com/archives/34)
- [FindBugs (Java)](http://findbugs.sourceforge.net/)
- [PMD](http://pmd.github.io/)
