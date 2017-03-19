---
layout: post
title: What is HTTP/2?
comments: True
excerpt_separator: <!--more-->
---

There are many reasons to feel excited about HTTP/2. It is the first major update of the HTTP protocol in **16 years!** It was long overdue as the web dramatically evolved over the years. HTTP/2 is aimed at making the web faster and overcome many shortcomings of HTTP/1.1. HTTP/2 brings advancements to speed, efficiency, standardization and security.

## HTTP/1.1: Simple Protocol, Complex Workarounds

HTTP was [envisioned](https://www.w3.org/Protocols/HTTP/AsImplemented.html) as a simple, application level, **request-response** protocol. Clients connect to servers and make HTTP requests asking for resource. Servers send response back and terminate the connection.

HTTP/1.1 was released in 1999 to keep up with the performance demands of late 90's [brochure-esque websites](http://www.smashingapps.com/2009/08/13/how-popular-website-designs-looked-like-in-late-90s.html). Its major improvement over its predecessor, the HTTP/1.0, was that it allowed a *connection to be reused* to send multiple requests and responses. This improves performance by eliminating connection setup overhead for each request.

<!--more-->

In 2004, Web 2.0 ushered in the new era of rich user experience and collaboration. It allowed users to interact with websites, leave comments, indulge in social sharing and enjoy many new features. [New technologies](https://en.wikipedia.org/wiki/Web_2.0#Technologies) emerged and many Web 2.0 websites like Wikipedia, Flickr and YouTube went on to become huge successes.

While all this was going on, the protocol that ran it all, HTTP/1.1, was having trouble keeping up. The websites were growing significantly in size and complexity and it wasn't designed to handle that. So smart people did what they do best: they invented workarounds, so-called best practices, to overcome HTTP/1.1 limitations. Hacks like [request pipelining](http://stackoverflow.com/questions/14810890/what-are-the-disadvantages-of-using-http-pipelining), [domain sharding](https://www.maxcdn.com/one/visual-glossary/domain-sharding-2/), [sprite sheets](https://developer.apple.com/library/iad/documentation/NetworkingInternet/Conceptual/SafariImageDeliveryBestPractices/ReducingHTTPRequestswithSprites/ReducingHTTPRequestswithSprites.html) and [data inlining](http://www.websiteoptimization.com/speed/tweak/inline-images/) were used to **optimize performance**. This added complexity on top of HTTP/1.1 and introduced regressions like unnecessary downloads, poor caching etc. Lack of standards meant that web developers had to deal with different browsers and versions. Ugh! I remember countless hours I sunk into tweaking websites to look great on both Firefox and Internet Explorer 7. It worked, but it was messy. And painful.

## The Journey to HTTP/2: A SPDY Stepping Stone

With no new development of HTTP/1.1 on the radar, Google took matters into their own very capable hands and started [**SPDY** (pronounced Speedy)](https://developers.google.com/speed/articles/spdy-for-mobile#methodology):

> SPDY is a replacement for HTTP, designed to speed up transfers of web pages, by eliminating much of the overhead associated with HTTP. SPDY supports **out-of-order responses, header compression, server-side push, and other optimizations** that give it an edge over HTTP when it comes to speed.

In reality, SPDY **didn't really replace HTTP/1.1** (and neither does HTTP/2 as we'll later see). Augmented would be the right word. SPDY sat on top of HTTP/1.1 and heavily modified the data transfer formats and connection handling.

Google released SPDY in 2010 in Chrome 6 and soon deployed SPDY across all Google services. Word spread and SPDY soon gained traction and support from the community and vendors like Mozilla, Nginx, Microsoft and Facebook. Internet Engineering Task Force (IEFT), responsible for HTTP standards, seized the opportunity and [published HTTP/2 standards in 2015](https://tools.ietf.org/html/rfc7540) deriving heavily from SPDY; SPDY's fingerprints are all over HTTP/2.

Google has announced that it will [stop supporting SPDY on May 15th, 2016](http://blog.chromium.org/2016/02/transitioning-from-spdy-to-http2.html). Adios SPDY, you will be remembered as an important stepping stone on the journey to HTTP/2.

## HTTP/2 Features

HTTP/2 is here. Every modern web browser now supports HTTP/2. All major cloud and CDN vendors support it. The adoption among websites isn't significant but it's growing. As of April 24, 2016, [7.2% of all websites use HTTP/2](http://w3techs.com/technologies/details/ce-http2/all/all).

HTTP/2 brings many improvements to HTTP/1.1 and the biggest ones you need to know are:

### 1. Multiplexing
Under HTTP/1.1, connections were persistent which allowed multiple requests to be sent or [pipelined](https://en.wikipedia.org/wiki/HTTP_pipelining) over the same TCP connection. While it improved performance by reducing connection establishment overhead, it wasn't a silver bullet. Even though multiple requests could be sent over the same connection, the responses must arrive synchronously in the same order as they were requested. This means an expensive resources (e.g. loading a large image file) will block a lightweight response if requested in the wrong order. This phenomenon is known as the [head-of-line (HOL) blocking](https://en.wikipedia.org/wiki/Head-of-line_blocking). In fact, pipelining was so poorly supported by web servers that many web browsers simply disabled it.

HTTP/2 allows multiplexing that solves HOL issue by allowing **responses to be arrived out of order**, thus eliminating the need to open multiple connections.

### 2. Compression & Metadata to Reduce Header Overhead
Each and every request and response under HTTP/1.1 has a header typically between 200 bytes to 2KB in size. The first issue is that the **headers are not permitted to be compressed**. Another issues with HTTP/1.1 headers is that they contain a lot of **redundant information** that is exchanged several hundred times as browsers make as many requests to load a webpage. Static headers like Accept* and User-Agent only need to be exchanged once.

HTTP/2 fixes both these problems by compressing and eliminating unnecessary headers. *High five.*

### 3. Server Push
HTTP/2 server can send data to a client before the client even asks for it. To understand why it is beneficial, let's understand how a webpage is loaded under HTTP/1.1: web browser requests a web page, waits for it to be downloaded, parses it to find all linked assets such CSS and JavaScript and then make *separate requests* to download these asserts.

HTTP/2 Server Push allows the server to send these files to the browser proactively with the first request knowing that it will need them to display the page. I'm sure there's a way to tell the server to stop being so proactive if the web browser already has files in its cache.

### 4. Encryption is *Not Really* Optional
People debated for a long time whether or not encryption (HTTPS) should be made **mandatory** in HTTP/2. In the end, the standards folks [decided not to make it mandatory](http://http2.github.io/faq/#does-http2-require-encryption), with a fair bit of warning:

> After extensive discussion, the Working Group did not have consensus to require the use of encryption (e.g., TLS) for the new protocol. However, some implementations have stated that they will only support HTTP/2 when it is used over an encrypted connection, and currently no browser supports HTTP/2 unencrypted.

So while the use of TLS is not imposed by the standard, browser vendors have made it a requirement. **Google Chrome and Mozilla Firefox have pledged to not support HTTP/2 without HTTPS**.

### 5. Binary Encoding
Unlike HTTP/1.1, HTTP/2 uses binary encoding. Without getting into binary vs text protocols debate, this means that HTTP/2 will be more efficient to parse and compact on the wire but will no longer be human readable. When I was learning HTTP, one of the first things I did was made a request by hand and looked at the response along with all the headers as it arrived in all its glory. Unfortunately, this won't be possible in HTTP/2 - at least not without specialized tools.

### 5. Backwards Compatibility
**HTTP/2 is backwards compatible** with HTTP/1.1. This means if you want to upgrade to HTTP/2, you could do so without changing anything. The upgrade will be equally seamless to your users. Don't forget to undo HTTP/1.1 performance optimizations (aka best practices) as they no longer provide the same benefits.

## HTTP/2 - Future is Fast... And Complex
People have [criticized](https://queue.acm.org/detail.cfm?id=2716278) HTTP/2 for being Google's idea, optimized for their needs and having needless complexity (things like flow controls to prevent DOS attacks). One has to agree that HTTP/2 is undoubtedly more complex than its predecessors, but the complexity is a necessary evil to keep up with today's needs. An average webpage in 2016 is the same [size as the original DOOM shareware binary](https://mobiforge.com/research-analysis/the-web-is-doom?r=1). Sure Google benefits from HTTP/2, but don't we all? It is time to move beyond HTTP/1.1 and I believe HTTP/2 is the answer.
