---
layout: post
title: Generating Sessions Ids
comments: True
excerpt_separator: <!--more-->
---

Session Id's are **unique, short-lived numbers** that servers assign to users when they log in (or visit) so they can remember (or track) users for the duration of their sessions. Servers use session Id's to remember users because the underlying protocol, HTTP, is [stateless](https://en.wikipedia.org/wiki/Stateless_protocol). To identify themselves, users need to present the session Id that was assigned to them in each subsequent request. For example, when you login to a website, it assigns you a session Id sends to your browser wrapped in a [cookie](https://en.wikipedia.org/wiki/HTTP_cookie). The browser **automatically sends the cookie back** in following requests so the server knows who is making the request.

Almost [all](https://docs.djangoproject.com/en/1.10/topics/http/sessions/) [web](http://docs.spring.io/spring-session/docs/current/reference/html5/) [frameworks](https://github.com/expressjs/session) I have worked with have built-in support for sessions: they generate and assign Id's under the hood. The only time I had to generate session Id's manually was when I was building a REST application (game service) that needed a custom way to identify users and sessions. This blog post is the result of research I had to do to build that feature. I would highly recommend not rolling out your custom session handling code, unless you absolutely have to.

<!--more-->

## Session Ids are unique, transient and non-guessable
1. Session Id's must be unique across all users. Can you imagine two people getting assigned the same Social Security number? [That would be a disaster](http://www.pcworld.com/article/3004654/government/a-tale-of-two-women-same-birthday-same-social-security-number-same-big-data-mess.html).

2. Session Id's have 'best-by' date and they *timeout* after a certain period. If they didn't, a hacker could steal and use them indefinitely. Generally the expiry period ranges from minutes to weeks. High-risk applications expire session Ids more frequently than the low risk ones to minimize the attack window.

3. Session Id's are not guessable. A bad example would be an algorithm that generates sequential session Id's. Hackers can easily identify patterns and hijack user sessions.  

You can generate and assign session Id's to users in many different ways. I'll discuss three common methods below.

## 1. Random session Ids

Random session Id's have no meaning by virtue of being completely random. The server sends them to the client and stores them in a database along with the the user information.

| Session Id                               | User Id     | Expiry Time |
|------------------------------------------|-------------|-------------|
| fb2e77d.47a0479900504cb3ab4a1f626d174d2d | jimHalpert1 | 15 minutes|

If session Id's are random numbers, how do we ensure that they cannot be **guessed** or predicted by hackers? In Cryptography theory, entropy is the measure of **uncertainty** associated with a random number. Session Id's should have *very* high entropy to protect against attacks. [OWASP](https://www.owasp.org/index.php/Session_Management_Cheat_Sheet#Session_Expiration) suggests at least 64 bits of entropy. Sounds complex? (it did to me.) The good news is that you and I should never have to worry about writing your own algorithms (Don't even think about it - random number generation is very complex). Most languages have [pseudo random numbers generators (PRNGs)](https://en.wikipedia.org/wiki/Pseudorandom_number_generator) that generate 'cryptographically secure' random numbers that have entropy. As an example, Tomcat uses SHA1**PRNG** to generate a random number and hash it with MD5 (see warning below) to create session Id's. Here's a link to the [source code](https://docs.jboss.org/jbossas/javadoc/4.0.2/org/jboss/web/tomcat/tc5/session/SessionIDGenerator.java.html) (There's a list of PRNGs for other languages near the end of this post).

**Warning**: Do NOT use MD5 because it is [considered insecure](https://security.stackexchange.com/questions/19906/is-md5-considered-insecure). Always use SHA1 or SHA2.

## 2. Session Id's using shared secret

These types of session Id's are conceived in such a way that the information needed to identify a user is embedded into them. Since session Id's are self-contained, the server **doesn't need to store them**. Let's look at a trivial algorithm that generates session Id's by combining username, IP address and a client secret:

```java
sessionId = SHA2(username + ipAddress + secretKey /* or salt */)
```

When a request arrives, it contains the username and IP address is automatically recorded. The server then uses the username, the IP address and secret key to *re-generate* the session Id and see if it matches with the session Id passed by the client. If it does, the verification is successful.

**Note**: If you use IP address to calculate session Id's, keep in mind that the session Id will be invalidated when the IP address changes. This happens very frequently if your users are on a mobile network and are moving. If you are not sure, don't use the IP address.

The **advantage** of this method is that the server doesn't have to maintain state and store session Id's in a database.

**Disclaimer:** The Session Id generation formula above is simplistic. Real applications would combine many parameters such as the user's access group, timestamp, etc. The timestamp is generated based on Session Id's lifetime to allow it to expire.

**Note:** There is a cool standard called [JSON Web Tokens](https://jwt.io/) that allows the payload to carry the information. I haven't used it but it looks promising.

## 3. Random session Id's with a predictable part

This is a slight modification of the Random session Id generation method. The session Id consists of both a random number and a hash combining some properties of the user such as the username and IP address.

```java
sessionId = SHA2(userId + ipAddr) + prngRandomNumber
```

The resulting session Id is stored in the session store and looked up for each request. I *feel* this is a little more secure than just using a (Cryptographically secure) random number. Over engineered? May be. But I'll err on the side of caution.

## Security

Before I end this article, let's briefly discuss security. Because **session Id's are usually portable**, as a developer, you need to ensure that they are not easily obtainable by eavesdroppers or can be shared by mistake.

1. Don't send session Id's unencrypted. Use HTTPS to encrypt all traffic end-to-end.
2. Don't send session Id's a URL parameter: your users can inadvertently share URL's thus revealing their session Id. Also, the session Id's will appear in the web server or application logs and will be visible to anyone who has access to logs.

## Bonus
Here is a small list of cryptographically secure number generators in popular languages:

1. [Java: SecureRandom](https://docs.oracle.com/javase/8/docs/api/java/security/SecureRandom.html)
2. [.NET: RNGCryptoServiceProvider](https://msdn.microsoft.com/en-us/library/system.security.cryptography.rngcryptoserviceprovider(v=vs.110).aspx)
3. [Node.js](https://www.npmjs.com/package/uid-safe)
4. [*nix: /dev/urandom](http://linux.die.net/man/4/urandom)

Update: 3/11/2017 - Removed references to MD5 thanks to [HollyGraceful](https://twitter.com/HollyGraceful) for calling it out.
