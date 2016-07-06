---
layout: post
title: Unit, Integration and End-To-End Tests - Finding the Right Balance
comments: True
excerpt_separator: <!--more-->
---

This is something that I have noticed on many projects that I have worked on: what some developers call unit tests are actually end-to-end tests in disguise. They write tests that start testing at the top-most level and check the last outcome. E.g. for a web server, **these types of tests start the server, initialize a HTTP client, pass the request and then check outermost response**. Developers like end-to-end tests:

- because it is an easy way to boost code coverage and everything including the database gets tested.
- end-to-end tests are the only way the system would be tested. This is a symptom of broader design issues.
- they can outsource or offload writing tests to Q/A or junior developers.

**The biggest drawback of end-to-end tests is that they fail to isolate failure**. For large systems, when tests fail, it's like finding a needle in the haystack. Because unit tests focus on small modules that are tested independently, they can pin-point the error which might save a lot of time that would be otherwise wasted in debugging and locating the problem. For the record, **I've nothing against end-to-end testing. I have an issue with only writing end-to-end tests and skipping unit and integration tests altogether**.

Another benefit that unit tests have over end-to-end tests is that they always work. No matter where you are. If I can download the run the project on my machine, I should be able to run unit tests. In contrast, end-to-end tests fail if some external component, database or messaging queue, is not available or cannot be reached. Unit tests allow developers to change the code or add new features with confidence. When I add a new feature to a large project and all unit tests pass, it gives me confidence that the new feature didn't break existing logic.

**The key is finding the right balance between unit, integration and end-to-end tests**. Integration tests have one major advantage over unit tests: **they ensure that modules that work well in isolation, also play well together**. Integration tests typically focus on a small number of modules and test their interactions.

So what's the right balance between unit, integration and end-to-end tests? According to [Google](http://googletesting.blogspot.co.uk/2015/04/just-say-no-to-more-end-to-end-tests.html):

>  To find the right balance between all three test types, the best visual aid to use is the testing pyramid. Here is a simplified version of the testing pyramid from the opening keynote of the 2014 Google Test Automation Conference:
>
![test_pyramid]({{ site.url }}/img/blogs/test_pyramid.png)
>
> The bulk of your tests are unit tests at the bottom of the pyramid. As you move up the pyramid, your tests gets larger, but at the same time the number of tests (the width of your pyramid) gets smaller.
>
> As a good first guess, **Google often suggests a 70/20/10 split: 70% unit tests, 20% integration tests, and 10% end-to-end tests**. The exact mix will be different for each team, *but in general*, it should retain that pyramid shape. **Try to avoid these anti-patterns**:
>
- **Inverted pyramid/ice cream cone. The team relies primarily on end-to-end tests, using few integration tests and even fewer unit tests**.
>
- **Hourglass. The team starts with a lot of unit tests, then uses end-to-end tests where integration tests should be used**. The hourglass has many unit tests at the bottom and many end-to-end tests at the top, but few integration tests in the middle.
