---
layout: post
title: Unit, Integration and End-To-End Tests - Finding the Right Balance
comments: True
excerpt_separator: <!--more-->
---

This is something I have regrettably noticed in many projects that I have worked on. Instead of a *balanced mix* of [unit](http://artofunittesting.com/definition-of-a-unit-test/) and other types of tests, **some projects rely primarily or entirely on end-to-end tests** (*E2E*). The *whole system* is tested by running real user scenarios and tests are performed at the outermost layers. E.g. for a web server, this means you **start the server, initialize a HTTP client, make a HTTP request and then check the outermost response**. By skipping unit and integration tests, we lose many benefits that these tests offer. What's worse is that people start calling these tests the "unit tests", just because they live inside the project and use [JUnit framework](http://junit.org/junit4/). Developers like end-to-end testing:

<!--more-->

- because it boost code coverage and everything including the database gets tested.
- because they can outsource or offload writing tests to Q/A or junior developers.
- because sometimes, it is the only way the system could be tested. However, this is a symptom of much broader design and architectural problem.

For the record, I've nothing against end-to-end testing. I have a problem with relying only on E2E testing to find bugs.

**The biggest drawback of end-to-end tests is that they cannot pin-point the root cause of failure**. Anything in the entire flow could have contributed to the error. In large and complex systems, it's like finding a needle in the haystack. Because unit tests focus on small modules that are tested independently, they can **identify the lines of code that caused the failure** with laser-sharp accuracy, which saves a lot of time.

Another nice thing about unit tests is that **they always work, and they are fast**. If I can build a project on my machine, I should be able to run its unit tests. In contrast, end-to-end tests would fail if some external component, like a database or a messaging queue, is not available or cannot be reached. And they take a long time to run. Unit tests allow developers to change the code or add new features with confidence. When I add a new feature to a large project and all unit tests pass, it gives me confidence that the new feature didn't break existing logic.

Between unit and end-to-end tests lie integration tests. They have one major advantage over unit tests: **they ensure that modules which work well in isolation, also play well together**. Integration tests typically focus on a small number of modules and test their interactions.

The key is finding the **right balance between unit, integration and end-to-end tests**. According to [Google](http://googletesting.blogspot.co.uk/2015/04/just-say-no-to-more-end-to-end-tests.html):

>  To find the right balance between all three test types, the best visual aid to use is the testing pyramid. Here is a simplified version of the testing pyramid [...]:
>
![test_pyramid]({{ site.url }}/img/blogs/test_pyramid.png)
>
> The bulk of your tests are unit tests at the bottom of the pyramid. As you move up the pyramid, your tests gets larger, but at the same time the number of tests (the width of your pyramid) gets smaller.
>
> As a good first guess, **Google often suggests a 70/20/10 split: 70% unit tests, 20% integration tests, and 10% end-to-end tests**. The exact mix will be different for each team, but in general, *it should retain that pyramid shape*. **Try to avoid these anti-patterns**:
>
- **Inverted pyramid/ice cream cone**. The team relies primarily on end-to-end tests, using few integration tests and even fewer unit tests.
>
- **Hourglass**. The team starts with a lot of unit tests, then uses end-to-end tests where integration tests should be used. The hourglass has many unit tests at the bottom and many end-to-end tests at the top, but few integration tests in the middle.

*70/20/10* split between unit, integration and end-to-end tests is a good, but general guideline. Each project will have a different mix. The **key is to retain the pyramid shape** of the testing pyramid, that is, `Unit > Integration > End-to-End Tests`.
