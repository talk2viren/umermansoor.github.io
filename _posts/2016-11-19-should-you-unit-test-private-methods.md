---
layout: post
title: Should You Unit Test Private Methods?
comments: True
excerpt_separator: <!--more-->
---

To unit test private methods or not to test, that's the question. There are two kinds of software developers in this world: those who *never* subject private methods to unit testing directly and those who do. Let's look at both sides of the arguments to understand this better.

<!--more-->

This [Stackoverflow question](http://stackoverflow.com/questions/105007/should-i-test-private-methods-or-only-public-ones?noredirect=1&lq=1) highlights the divide. The accepted answer says:

> **I do not unit test private methods. A private method is an implementation detail that should be hidden to the users of the class**. Testing private methods breaks encapsulation.
>
If I find that the private method is huge or complex or important enough to require its own tests, I just put it in another class and make it public there

User Dave Sherohman [disagrees](http://stackoverflow.com/a/105209):

> [...] Personally, my primary use for code tests is to ensure that future code changes don't cause problems and to aid my debugging efforts if they do. I find that testing the private methods just as thoroughly as the public interface (if not more so!) furthers that purpose.
>
Consider: You have public method A which calls private method B. A and B both make use of method C. C is changed (perhaps by you, perhaps by a vendor), causing A to start failing its tests. Wouldn't it be useful to have tests for B also, even though it's private, so that you know whether the problem is in A's use of C, B's use of C, or both?

Another user [writes](http://stackoverflow.com/questions/34571/how-to-test-a-class-that-has-private-methods-fields-or-inner-classes?noredirect=1&lq=1#comment76873_34586):

> [...] It's totally valid to have an algorithm in a private method which needs more unit testing than is practical through a class's public interfaces.

Personally, I like encapsulation and avoid writing unit tests for private methods. If the design is right, most of the functionality provided by private methods is tested implicitly by testing public method. If I get to a point where I need to write unit tests for private methods, I take that as a sign that my design needs refactoring and the private method needs to be extracted into its own class. But sometimes, breaking away a new class isn't easy, and it might end up introducing more complexity to the design. Or you are working on legacy code and aren't allowed to or don't want to change the design. If that's the case, it's alright to go ahead and write a unit test for that private method.
