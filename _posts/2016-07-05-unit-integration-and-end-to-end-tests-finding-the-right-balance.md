---
layout: post
title: Unit, Integration and End-To-End Tests - Finding the Right Balance
comments: True
excerpt_separator: <!--more-->
---

This is something I have sadly noticed in many projects that I have worked on. Instead of a mixture of [unit](http://artofunittesting.com/definition-of-a-unit-test/) and other types of tests, some projects primarily rely only on end-to-end tests (**E2E**). In E2E, the *whole system* is tested by performing some high-level action and checking the result that the system produces. E.g. for a web server, this means you **start the server, initialize a HTTP client, make a HTTP request and then check the outermost response**. By skipping unit and integration tests, we lose many benefits that these tests offer, as we'll see in a minute. What's worse is that people start calling these tests the "unit tests" just because they live inside the project and use [JUnit framework](http://junit.org/junit4/). Developers like end-to-end testing:

- because it boost code coverage and everything including the database gets tested.
- because they can outsource or offload writing tests to Q/A or junior developers.
- because sometimes, it is the only way the system could be tested. However, this is a symptom of much broader design and architectural problem.

<!--more-->

**The biggest drawback of end-to-end tests is that they cannot isolate failure**. For large systems, when these tests fail, it's like finding a needle in the haystack. Because unit tests focus on small modules that are tested independently, they can **pin-point the error** which saves a lot of time. For the record, I've nothing against end-to-end testing. I have an issue with **only writing end-to-end tests** and skipping unit and integration testing.

Another benefit that unit tests have is that **they always work, and they work fast**. No matter where you are. If I can build the project on my machine, I should be able to run the unit tests. In contrast, end-to-end tests fail if some external component, like a database or a messaging queue, is not available or cannot be reached. Another common problem with end-to-end tests is that they often take a long time to run: test client waits for a few seconds before timing out and failing the test, provisioning external resources, etc. Unit tests allow developers to change the code or add new features with confidence. When I add a new feature to a large project and all unit tests pass, it gives me confidence that the new feature didn't break existing logic.

On the other hand, integration tests have one major advantage over unit tests: **they ensure that modules that work well in isolation, also play well together**. Integration tests typically focus on a small number of modules and test their interactions. **The key is finding the right balance between unit, integration and end-to-end tests**. According to [Google](http://googletesting.blogspot.co.uk/2015/04/just-say-no-to-more-end-to-end-tests.html):

>  To find the right balance between all three test types, the best visual aid to use is the testing pyramid. Here is a simplified version of the testing pyramid from the opening keynote of the 2014 Google Test Automation Conference:
>
![test_pyramid]({{ site.url }}/img/blogs/test_pyramid.png)
>
> The bulk of your tests are unit tests at the bottom of the pyramid. As you move up the pyramid, your tests gets larger, but at the same time the number of tests (the width of your pyramid) gets smaller.
>
> As a good first guess, **Google often suggests a 70/20/10 split: 70% unit tests, 20% integration tests, and 10% end-to-end tests**. The exact mix will be different for each team, *but in general*, it should retain that pyramid shape. **Try to avoid these anti-patterns**:
>
- **Inverted pyramid/ice cream cone**. The team relies primarily on end-to-end tests, using few integration tests and even fewer unit tests.
>
- **Hourglass**. The team starts with a lot of unit tests, then uses end-to-end tests where integration tests should be used. The hourglass has many unit tests at the bottom and many end-to-end tests at the top, but few integration tests in the middle.
