---
layout: post
title: |
    Tweaking TCP for Real-time Applications: Nagle's Algorithm and Delayed Acknowledgment
comments: True
excerpt_separator: <!--more-->
---

[TCP](https://en.wikipedia.org/wiki/Transmission_Control_Protocol) is a *complex* protocol.

Don't get me wrong. It is a marvelous piece of engineering that gives us the reliable data transmission guarantee that other protocols don't provide. Reliable data transmission between two devices on the internet is no walk in the park and TCP uses a [lot](https://en.wikipedia.org/wiki/Transmission_Control_Protocol#Flow_control) [of](https://en.wikipedia.org/wiki/TCP_congestion_control) [magic](https://en.wikipedia.org/wiki/TCP_window_scale_option) under the hood to make things happen. Generally, it does a fine job of [abstracting low level details](https://codeahoy.com/2016/05/06/good-abstractions-have-fewer-leaks/) and its default settings work fine for most TCP applications. However, once in a while, things don't go according to plan and we need to pop open the hood and tweak some parameters. It is in these situations, that some knowledge of TCP comes in very handy.

 <!--more-->

When I joined [Glu](https://www.glu.com/), my first project was to design and build a TCP server (a horizontally scalable cluster of servers really) that sits between mobile devices and backend gaming services so they could exchange messages with one another. *Edge Server*, as it was called, had to support low message latency and support tens of thousands of connections on a single (Amazon EC2) instance. I had written a couple of high-throughput TCP servers before, but never had to support thousands of concurrent connections. I was excited.

[Netty](https://netty.io/) was chosen as the underlying framework and Edge Server was born. Everything looked fine on the surface... until we started load and performance testing. The message latency was worse than what I had expected. It wasn't something that real-time multiplayer games were willing to tolerate. After ruling out performance issues in the Java application, I decided it was time to pop open the hood and see what's going on underneath with the TCP.

I selected a not-so-fine weekend for my investigation (It had continued to rain all through that week and got worse on the weekend.) After a while of running few additional tests and looking at the network traffic in [Wireshark](https://www.wireshark.org/), a pattern began to emerge: instead of sending messages to the client immediately, the server was **buffering** and sending a bunch of messages **bundled** together in one packet.

It took me a few minutes to find the root cause of why the latency was higher than expected. Now, I had to figure out how to *disable* buffering.

![imbuffering]({{ site.url }}/img/imbuffering.jpg)

My first (naive) thought was that TCP must be buffering packets in its send buffer and if I could somehow force it to 'flush' its buffer every time I add a message to it, the buffering problem will go away. But there is no API call to force TCP to flush its send buffer. In fact, the TCP cannot be forced to send buffered data out. It makes up its own mind as to when to send data. There must be something else. Back to research... and a few Google searches later, I had found my answer as to what caused the TCP to buffer data: something called the **Nagle's algorithm**.

## Nagle's Algorithm

If there's no congestion, TCP is supposed to send data out as soon as it receives it from the application, after tacking on a header. If the application is generating a lot of small messages, the headers can add a lot of overhead (Each TCP+IP header is 40 bytes so if we are sending 1-byte of data, it's sent as 41-byte packet on the network.) Nagle's algorithm is way to reduce the overhead by combining many small messages into a single message. In other words, **it is a technique to make TCP more efficient by reducing the number of packets that are sent over the network**.

Nagle's algorithm works great for most TCP applications like video streaming or the high-volume network sniffer I wrote many years ago, which produced data at a very high rate for a few consumers (TCP clients.) But it doesn't work in this case where, even though Edge Server has tens of thousands of concurrent clients, there's only a small amount of data (a chat message ~ 100 bytes) for each client.

Luckily, Nagle's algorithm can be turned off easily by setting the [TCP_NODELAY](http://download.java.net/jdk7/archive/b123/docs/api/java/net/SocketOptions.html#TCP_NODELAY) flag in the application. Once Nagle's algorithm was disabled, the latency improved and was at an acceptable levels.

So if you are building **TCP applications that expect responses to arrive in real-time, Nagle's algorithm (turned on by default) will result in poor performance and higher latency**. You should consider disabling it using the TCP_NODELAY socket option.

I could end this blog post here. However, it won't be fair as it paints Nagle's algorithm in poor light and positions it as the main culprit. Is it? To answer that, let's look at the [Nagle's algorithm](https://en.wikipedia.org/wiki/Nagle's_algorithm):

```
if there is new data to send
  if the window size >= MaximumSegmentSize and available data is >= MaximumSegmentSize
    send complete MaximumSegmentSize segment now
  else
    if there is unconfirmed data still in the pipe
      enqueue data in the buffer until an acknowledge is received
    else
      send data immediately
    end if
  end if
end if
```

So what the algorithm is saying is that if the data to be sent is *smaller* than the [maximum segment size](https://en.wikipedia.org/wiki/Maximum_segment_size) (~ 1.4 KB), it is sent immediately *ONLY* if TCP has received acknowledgment for all the data that was previously sent. Another way to see it: if the newly generated data on the sender is small and its TCP is waiting for the receiver to acknowledge receipt of data that is in flight, Nagle's algorithm will tell TCP to buffer the data and it won't be sent immediately.

Armed with this information, Nagle's algorithm doesn't look so bad. After all, a TCP receiver should acknowledge data as soon as it receives it. At least that's what I had assumed. However, I was *wrong*. Another TCP optimization technique known as the *TCP delayed acknowledgment* makes clients buffer acknowledgments instead of sending them as soon as the data is received. Let's look at it in more detail.

## TCP Delayed Acknowledgment

TCP delayed acknowledgment is another optimization technique to combine multiple acknowledgments (ACKs) into a single response thus reducing the overhead. Upon receiving data, TCP receivers (using delayed acknowledgment) don't send an acknowledgment right away but instead wait for a few hundred milliseconds (200ms to 500ms) so it can be sent bundled together with any other acknowledgments or data that it might generate in during that window.

If you think about it carefully, a picture begins to emerge: **Nagle's algorithm on the sender and TCP delayed acknowledgement on receiver is a double whammy for real-time applications**: receivers wait a few hundred milliseconds before acknowledging senders. Without receiving acknowledgement, senders keep on buffering small packets until they receive acknowledgement or the buffered data exceeds maximum segment size, which can take a long time (> 200ms) if data to sent is small. This *double buffering* - once on the sender (Nagle) and then on the receiver (delayed acknowledgment) - wreaks havoc on real-time applications and increases message latency.

Nagle's algorithm would probably perform better without the TCP delayed acknowledgment, however, it may not always be possible to control behavior of client devices so disabling Nagle's algorithm on the server for real-time applications that exchange short messages, is the way to go. While we are on this topic, it *is* possible to turn off delayed acknowledgment: you can disable it using the [TCP_QUICKACK](TCP_QUICKACK) socket option in your application. Unfortunately, [Java doesn't support it yet](https://bugs.openjdk.java.net/browse/JDK-8145635) :(

Hope you enjoyed. Until next time!
