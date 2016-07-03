---
layout: post
title: Idempotent and Safe HTTP Methods - Why Do They Matter?
comments: True
excerpt_separator: <!--more-->
---

Idempotency and safety are two very important properties of HTTP methods. For developers building RESTful APIs over HTTP, it is important to understand what these properties are, why do they matter and how to correctly implement them. [Idempotency](https://en.wikipedia.org/wiki/Idempotence) is used to describe an:

> operation that will produce the **same results if executed once or multiple times**.

<!--more-->

As an example of an idempotent request, it doesn't matter how many times I submit a request to set my city of residence to 'San Francisco'. The final outcome will be the same: the city field in the database is updated to 'San Francisco'. However, **a request to post a new message isn't idempotent**: the same message will be stored or sent multiple times if the request is repeated, resulting in duplication.

**Idempotent operations might alter the state of some resource** but no matter how many times the operation is repeated, **the state does not change any further**. Some idempotent operations have a *special property*: they do not modify the state on the server side at all. In other words, **some idempotent operations have absolutely zero side-effects**. For example, a query to retrieve my current city doesn't change the database. These types of operations are given a name: **safe** or **[nullipotent methods](http://www.less-broken.com/blog/2011/07/why-you-should-care-about-idempotence.html)**:

> Related is the idea of nullipotence: **a function is nullipotent if not calling it at all has the same side effects as calling it once or more**. In practice, this simply means that the function doesn’t have any side effects at all. A database query saying “get row 42” is a good example. Nullipotence is clearly a stronger condition than idempotence.


## Idempotency is a Contract

During interviews, when asked about idempotency and safety (nullipotentcy) of HTTP methods, most people describe them correctly. But many stumble when we dig deeper into why these properties matter?

Idempotency and safety are *guarantees* that server applications make to their clients and the world. It is a **[contract defined by the HTTP](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)** standard that developers must adhere to when implementing RESTful APIs over HTTP. An operation doesn't **automatically become idempotent** or safe just because it is invoked using the `GET` method, if it isn't implemented in an idempotent manner. A poorly written server application might use `GET` methods to update a record in the database or to send a message to a friend (I have seen applications that do this.) This is a really, really bad design that will have unintended consequences.

**Adhering to the idempotency and safety contract helps make an API fault-tolerant and robust**. Clients, middleware applications and various servers that requests pass through before reaching your application, **use this contract for various optimizations**. Clients may automatically cancel a `GET` request if it is taking too long to process and repeat it because they assume it has the same effect (since `GET` is idempotent). However, they won't do the same thing for `POST` requests because the first one may have already altered some state on the server side. This is the reason why web browsers display a warning message that you are about to re-submit a form when you hit the back button to go to a form (For this reason, always redirect after a successful `POST` operation.)
In the same veins, cache servers [don't cache](http://stackoverflow.com/questions/626057/is-it-possible-to-cache-post-methods-in-http) POST requests.

In summary, when building RESTful applications using HTTP, it is important to implement HTTP methods in a manner that **satisfies their idempotency and safety contract, because clients and intermediaries are free to use this contract to optimize and enhance the user experience**. Dont use `GET` method for operations that alter the database and don't use `POST` to retrieve information ([with one exception](https://blogs.dropbox.com/developers/2015/03/limitations-of-the-get-method-in-http/)). The table below lists a few HTTP methods and their idempotency and safety guarantees as defined in the contract:

| HTTP Method | Idempotent?      | Safe?     |
| ----------- | ---------------  | --------- |
| GET         | yes              | yes       |
| HEAD        | yes              | yes       |
| PUT         | yes              | NO        |
| DELETE      | yes              | NO        |
| POST        | NO               | NO        |
