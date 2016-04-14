---
layout: post
title: Generating Sessions Ids
comments: True
excerpt_separator: <!--more-->
---

Session Identifiers are a bit like Social Security numbers for the web.
![Sessions and SSN]({{ site.url }}/img/sessionandssn.png)

Session Identifiers (aka Session Ids or Session Tokens) are [unique identifiers](https://en.wikipedia.org/wiki/Unique_identifier) that are given to users when they log in. All subsequent requests from users has this Session Id that allows the server look up and identify users.

Session Ids exist because HTTP is a stateless protocol. It has no concept of a user session. Each request is treated as an independent transaction that is unrelated to any previous transaction. This is true for all requests even the ones made over the same connection. Session Ids provide a way to associate request to a users.

Session Ids are typically stored in a 'cookie' that your browser automatically sends to the web server with each request. If you are building a REST application, you would generate Session Ids  (or their cousin the Access token) on the server   to allow third-party applications to access your APIs.

<!--more-->

## Session Ids are unique, transient and non-guessable
1. Session Ids must be unique across all users. Can you imagine two people getting assigned the same Social Security number? [That would be a disaster.](http://www.pcworld.com/article/3004654/government/a-tale-of-two-women-same-birthday-same-social-security-number-same-big-data-mess.html).

2. Session Ids have 'best-by' date and they *timeout* after a certain period. If Session Ids didn't expire, a hacker who is able to steal, could use them indefinitely. Generally the expiry period ranges from minutes to weeks. High-risk applications expire Session Ids more frequently than the low risk ones to minimize the attack window.

3. Session Ids are not guessable. A bad example of Session Id generation algorithm is one that generates sequential Session Ids. Hackers can easily identify the patterns and hijack user sessions.  

Three common ways of generating Session Ids are:

## 1. Random Session Id generation
Random Session Ids are random and have no meaning. In addition to sending it to client, the server also stores the Session Id in its database. If Session Ids aren't stored, the server will have no way of telling who the user is just by looking at the Session Id.

| Session Id                               | User Id     | Expiry Time |
|------------------------------------------|-------------|-------------|
| fb2e77d.47a0479900504c(b3ab4a1f626d174d2d) | jimHalpert1 | 15 minutes|

*How do we ensure that Session Ids cannot be guessed or predicted by hackers?* In Cryptography theory, entropy is the measure of **uncertainty** associated with a random number. Session Ids should have *very* high entropy. [OWASP](https://www.owasp.org/index.php/Session_Management_Cheat_Sheet#Session_Expiration) suggests at least 64 bits of entropy. In other words, Session Ids should be large and very random numbers.

Random number generation is very complex. The good news is that we should never have to worry about writing our own random number algorithms (Don't even think about it). Most languages have [pseudo random numbers generators or PRNGs](https://en.wikipedia.org/wiki/Pseudorandom_number_generator) that generate 'cryptographically secure' random numbers (Please don't "design" your own algorithm). Tomcat uses SHA1PRNG to generate a random number and hash it with MD5 to create Session Ids. Here's a link to the [source code](https://docs.jboss.org/jbossas/javadoc/4.0.2/org/jboss/web/tomcat/tc5/session/SessionIDGenerator.java.html) (There's a list of PRNGs for other languages near the end of this post).

## 2. Self-Contained Session Ids

These types of Session Ids are conceived in such a way that the information needed to identify a user is embedded into them. Since Session Ids are self-contained, the server doesn't need to store them in a session store. Let's look at a trivial example. Suppose that we are writing a simple algorithm that generates Session Ids by combining username, IP address and a client secret:

```java
sessionId = MD5(username + ipAddress + serverSecretKey /* or salt */)
```
When a request arrives, the server *re-generates* the Session Id and matches it with the Session Id passed by the client for verification.

This type of Session Id generation is common in many custom Authentication and Authorization servers.

**Disclaimer:** The Session Id generation formula above is simplistic. Real applications would combine many parameters such as the user's access group, time stamp, etc. The timestamp is generated based on Session Id's lifetime to allow it to expire.

**Note:** There is a cool standard called [JSON Web Tokens](https://jwt.io/) that allows the payload to carry the information. I haven't used it but it looks promising.

## 3. Random Session Ids with a predictable part

This is a slight modification of the Random Session Id generation method. In this case, the Session Id consists of both a random number and a hash combining some properties of the user such as the username and IP address.

```java
sessionId = MD5(userId + ipAddr) + prngRandomNumber
```

The resulting Session Id is stored in the session store and looked up for each request. I feel this is a little more secure than just using a (Cryptographically secure) random number. Over engineered? May be. But I'll err on the side of caution.

If you generate Session Ids using any of the methods above, encode it using Base-64 to make it  [URL Safe](http://www.blooberry.com/indexdot/html/topics/urlencoding.htm). (Or you could use other URL Encoding schemes).

## Summary

In this post, we looked at what Session Identifiers are and how they are used. We also looked at few methods of generating Session Ids. Most languages and frameworks support Session Id generation so please check if the framework you are using already has this before rolling out your own implementation.

## Bonus
Here is a small list of cryptographically secure number generators in popular languages:

1. [Java: SecureRandom](https://docs.oracle.com/javase/8/docs/api/java/security/SecureRandom.html)
2. [.NET: RNGCryptoServiceProvider](https://msdn.microsoft.com/en-us/library/system.security.cryptography.rngcryptoserviceprovider(v=vs.110).aspx)
3. [Node.js](https://www.npmjs.com/package/uid-safe)
4. [*nix: /dev/urandom](http://linux.die.net/man/4/urandom)

<p class="message">
We love to hear your feedback, comments or thoughts. Please leave a comment below.
</p>
