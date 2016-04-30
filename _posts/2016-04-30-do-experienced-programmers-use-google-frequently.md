---
layout: post
title: Do Experienced Programmers Use Google Frequently?
comments: True
excerpt_separator: <!--more-->
---


Brad was a good software developer. He had over 15 years of experience and programmed in C++, Java, Ruby and C#. But every time I and other *rookies* walked by his desk, it was hard not to notice 10+ open tabs of Google searches full of StackOverflow results. Gossip ensued: "Is Brad *really* a good programmer or is he just a good at *googling* solutions?"

Software developers, especially those who are new to the field, often [ask](http://two-wrongs.com/how-much-does-an-experienced-programmer-use-google) or  [wonder](http://www.hanselman.com/blog/AmIReallyADeveloperOrJustAGoodGoogler.aspx) about this. Let me break it to you: **Experienced programmers use Google... a lot**. In fact, one might argue they **use it more than the beginners**. Using Google doesn't make them bad programmers or imply that they cannot code without Google. In fact, truth is quite the opposite: Google is an essential part of their software development toolkit and they know when and how to use it.

<!--more-->

A big reason to use Google is that it is hard to remember all those minor details and nuances especially when you are programming in multiple languages and using dozens of frameworks. As Einstein said:

> “Never memorize something that you can look up.” - Albert Einstein

Aside from that, good programmers know that they cannot be the first one to have encountered a problem. They use Google to research possible solutions, carefully evaluating the results and consciously separating the wheat from the chaff; they don't blindly follow or copy-paste any solution they come across. Expert programmers are also paranoid, living in self-doubt and [questioning their competence](http://blog.valbonne-consulting.com/2014/08/16/the-imposter-syndrome-in-software-development/). Whenever their spidey senses start tingling, they know they may be going the wrong hole; So they rely on Google on validate their logic.

Recently, I had to write web server using [Netty](http://netty.io/) in Java to handle persistent sockets from mobile games. I had never used Netty before. Here are my Google searches I did:

```
1. netty tutorial
2. netty maven dependency
3. netty bytebuf to string
4. netty bytebuf release
5. netty 4 changes
6. setOption("child.bufferFactory") netty 4  
7. ByteBuf netty
8. opensource projects using netty framework  
9. netty 4 examples
10. netty 4 adding json encoder
11. netty channel pipeline
12. netty 4 messagetomessage encoder
13. netty serverbootstrap childhandler
14. ByteBuf netty
15. lengthfieldbasedframedecoder netty 4
16. netty 4 client examples
17. netty 4 bytebuf to bytebuffer
18. netty 4 endianness
19. netty channelhandlercontext
20. netty channelhandlercontext thread safe
21. netty user authentication
22. netty heartbeat handling
23. load test netty with 10k concurrent sockets
```

I wrote 255 lines of code that included a working server and a client. I queried google 23 times mostly landing on StackOverflow, Netty 4 website, GitHub, and JavaDocs. If you do the math, that averages out to **1 query every 10 lines of code**.

So sit back, relax and remember that **Google is software developer's best friend**.
