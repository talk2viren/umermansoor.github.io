---
layout: post
title: Idempotent and Safe HTTP Methods - Why Do They Matter?
comments: True
excerpt_separator: <!--more-->
---

If you are designing or building REST APIs, you should be aware of two very important properties of HTTP methods: **idempotency and safety**. These properties are defined in the [HTTP specification](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). I'm calling them properties, but '**guarantees'** would be a better term: you don't automatically get them; you actually need to design for these guarantees because your clients expect you to follow the contract. Let's get the definitions out of the way first and then we'll look at the contract and why it's important to stick to it.

<!--more-->

## Definitions

An operation is **idempotent if it will produce the same results when executed once or multiple times**. For example, it doesn't matter how many times I submit a request to set my current location to 'San Francisco'. The final outcome will be the same: the city field in the database is set to 'San Francisco'. On the other hand, a request to POST a new message to the forum is *not idempotent*: the same message will be stored or sent multiple times if the client. Some people, wrongly, assume that for a request to be idempotent, the same response must be sent back to the client each time: idempotency has nothing to do with the response that's sent back to the client. It's a server side guarantee ensuring that the **state of the resource on the server** does not change any further after the first request, no matter how many times the request is duplicated.

Some idempotent operations have an additional, *special property*: they do not modify the state on the server side at all. Simply put, these methods are read-only and have absolutely zero side-effects. For example, a query to retrieve my current city doesn't change the database. These types of operations are given a *special* name: **safe** or **[nullipotent methods](http://www.less-broken.com/blog/2011/07/why-you-should-care-about-idempotence.html)**:

> Related is the idea of nullipotence: **a function is nullipotent if not calling it at all has the same side effects as calling it once or more**. In practice, this simply means that the function doesn’t have any side effects at all. A database query saying “get row 42” is a good example. Nullipotence is clearly a stronger condition than idempotence.

Here's a list of the most commonly used HTTP methods and whether they are idempotent and/or safe as defined by the contract:

| HTTP Method | Idempotent?      | Safe?     |
| ----------- | ---------------  | --------- |
| GET         | yes              | yes       |
| HEAD        | yes              | yes       |
| PUT         | yes              | NO        |
| DELETE      | yes              | NO        |
| POST        | NO               | NO        |

## Why idempotency matters?

Idempotency and safety (nullipotentcy) are *guarantees* that server applications make to their clients and the world. It is a **[contract defined by the HTTP](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) standard that developers must adhere to** when implementing RESTful APIs over HTTP. An operation doesn't **automatically become idempotent** or safe just because it is invoked using the `GET` method, if it isn't implemented in an idempotent manner. A poorly written server application might use `GET` methods to update a record in the database or to send a message to a friend (I have seen applications that do this.) This is a really, really bad design.

**Adhering to the idempotency and safety contract helps make an API fault-tolerant and robust**. Clients, middleware applications and various servers that requests pass through before reaching your application, **use this contract for various optimizations**. Clients may automatically cancel a `GET` request if it is taking too long to process and repeat it because they assume it has the same effect (since `GET` is idempotent). However, they won't do the same thing for `POST` requests because the first one may have already altered some state on the server side. This is the reason why web browsers display a warning message that you are about to re-submit a form when you hit the back button to go to a form (For this reason, always redirect after a successful `POST` operation.)
In the same veins, cache servers [don't cache](http://stackoverflow.com/questions/626057/is-it-possible-to-cache-post-methods-in-http) POST requests and safe methods can pre-fetched to stored in cache to enhance performance.

In summary, when building RESTful applications using HTTP, it is important to implement HTTP methods in a manner that **satisfies their idempotency and safety contract, because clients and intermediaries are free to use this contract to optimize and enhance the user experience**. Don't use `GET` method for operations that alter the database and don't use `POST` to retrieve information ([with one exception](https://blogs.dropbox.com/developers/2015/03/limitations-of-the-get-method-in-http/)).

See you next time.
