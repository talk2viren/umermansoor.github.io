---
layout: post
title: Unit, Integration and End-To-End Tests - Finding the Right Balance
comments: True
excerpt_separator: <!--more-->
---

This is something I have regrettably noticed in many backend projects that I have worked on. Developers write "unit tests" that in reality are 'end-to-end' tests. They test the entire flow of the application from start to the end. There is no isolation of units and **the notion of the unit is the whole system**, along with all of its external dependencies like databases, queues, caches, and other services. For a web server project, these tests start the server, initialize a HTTP client, make a HTTP request and check the response to make sure it has all the expected information. If so, the test is declared a success. By treating the whole system as a unit and not testing independent units in isolation and their interplay, we loose many benefits that unit and integration tests offer.

<!--more-->

Technically speaking, these developers aren't violating the definition or principles of unit testing. Unit testing is [ill-defined](http://martinfowler.com/bliki/UnitTest.html). I don't claim to be an expert, but in my humble opinion:

- Unit testing should focus on testing small units (typically a Class or a complex algorithm).
- Units should be tested in isolation and independent of other units. This is typically achieved by [mocking](https://en.wikipedia.org/wiki/Mock_object) the dependencies.
- Unit tests should be fast. Usually shouldn't take more than a few seconds to provide feedback.

Most projects benefit from having a **balanced mix of various automated tests to capture different types of errors**. The exact composition of the mix varies depending on the nature of the project, as we'll see later.

End-to-end tests are good at capturing certain kinds of bugs, but their **biggest drawback is that they cannot pin-point the root cause of failure**. Anything in the entire flow could have contributed to the error. In large and complex systems, it's like finding a needle in the haystack: you'll find the root cause, but it will take time. Because unit tests focus on small modules that are tested independently, they can **identify the lines of code that caused the failure** with laser-sharp accuracy, which can save a lot of time.

Another nice thing about unit tests is that **they always work, and they work fast**. Unlike end-to-end tests that rely on external components, **unit tests are not flaky**. If I can build a project on my machine, I should be able to run its unit tests. In contrast, end-to-end tests would fail if some external component, like a database or a messaging queue, is not available or cannot be reached. And they can take a lvery ong time to run.

Unit tests allow developers to **refactor and add new features with confidence**. When I'm refactoring a complex project that has well-written unit tests, I run them often, usually after every small change. In a matter of few seconds, I know whether I broke something or not. Even better, a failing test usually prints a nice message telling me what broke: whether some [GuardAssertion](http://xunitpatterns.com/Guard%20Assertion.html) failed or the expected response was off by one, helps me isolate the failure.

Between unit and end-to-end tests lie integration tests. They have one major advantage over unit tests: **they ensure that modules which work well in isolation, also play well together**. Integration tests typically focus on a small number of modules and test their interactions.

The key is to find the **right balance between unit, integration and end-to-end tests**. According to [Google's Testing Blog](http://googletesting.blogspot.co.uk/2015/04/just-say-no-to-more-end-to-end-tests.html):

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

*70/20/10* split between unit, integration and end-to-end tests is a good, general rule of thumb. If a project has large number of integrations or complex interfaces, it should have more integration and end-to-end tests. A project that is primarily focused on computation or data, should have more unit tests and fewer integration tests. The right mix depends on the nature of the project but the **key is to retain the pyramid shape** of the testing pyramid, that is, `Unit > Integration > End-to-End Tests`.
