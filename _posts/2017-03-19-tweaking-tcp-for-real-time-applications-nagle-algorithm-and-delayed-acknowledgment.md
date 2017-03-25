---
layout: post
title: |
    Tweaking TCP for Real-time Applications: Nagle's Algorithm and Delayed Acknowledgment
comments: True
excerpt_separator: <!--more-->
---

[TCP](https://en.wikipedia.org/wiki/Transmission_Control_Protocol) is a *complex* protocol.

Don't get me wrong. It is a marvelous piece of engineering that gives us the reliable data transmission guarantee that other protocols don't provide. *Reliable* data transmission between two devices on the internet is no walk in the park and TCP uses a [lot](https://en.wikipedia.org/wiki/Transmission_Control_Protocol#Flow_control) [of](https://en.wikipedia.org/wiki/TCP_congestion_control) [magic](https://en.wikipedia.org/wiki/TCP_window_scale_option) under the hood to make things happen. Generally, it does a fine job of [abstracting away low level details](https://codeahoy.com/2016/05/06/good-abstractions-have-fewer-leaks/) and its default settings work fine for most general purpose use cases. However, once in a while, things don't go according to plan and we need to pop open the hood and do some tweaking. It is in these situations, that some knowledge of TCP comes in very handy.

 <!--more-->

By default, **TCP uses two buffering techniques** to optimize and minimize overhead for general purpose applications. However, if you are building applications that require real-time message delivery for small messages (e.g. chat or control messages), you must have some knowledge of these techniques.

1. Nagle's algorithm
2. TCP delayed acknowledgment

Let's look at them in more detail.

## Nagle's Algorithm

If there's no congestion, TCP tacks on a header and sends data out as soon as it gets it from the application. If the application is generating a lot of small messages, the headers can add a lot of **overhead**: TCP/IP headers are 40-byte, so 1-byte of data is sent as 41-byte packet on the network. A computer programmer named John Nagle came up with an algorithm to reduce the overhead by combining many small messages into a single message. Nagle's algorithm, named after its inventor, **is a technique to make TCP more efficient by reducing the number of packets that are sent over the network**. Here's the [pseudo code]((https://en.wikipedia.org/wiki/Nagle's_algorithm)) for the algorithm:

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

So what the algorithm is saying is that if the data to be sent is *smaller* than the [maximum segment size (MSS)](https://en.wikipedia.org/wiki/Maximum_segment_size) (~ 1.4 KB), it is sent immediately *ONLY* if TCP has received acknowledgment for all the data that was previously sent. Another way to see it: if the newly generated data on the sender is small and its TCP is waiting for the receiver to acknowledge receipt of data that is in flight, Nagle's algorithm will tell TCP to buffer the data and it won't be sent immediately.

Nagle's algorithm works great for most TCP applications like video streaming that produce data at a very high rate which exceeds the MSS very quickly and has to be sent out. But for multiplayer gaming servers, it creates performance issues. Tens of thousands of clients connect to a front-end server which sits between clients and backend gaming services. Even though the overall throughput is very high, only a small amount of data (a chat message roughly 100 bytes) is available for each client/socket and hence the data is buffered using Nagle's algorithm.

So if you are building **TCP applications that expect responses to arrive in real-time, Nagle's algorithm will result in poor performance and higher latency**. Nagle's algorithm is turned on by default at the system level, but you can disable it for you application using [TCP_NODELAY](http://download.java.net/jdk7/archive/b123/docs/api/java/net/SocketOptions.html#TCP_NODELAY) socket option.

However, Nagle's algorithm is not the only culprit when it comes to higher message latencies for real-time application. As we're about to see, another type of buffering on the receiver side, prevents acknowledgments for received data to be sent out immediately.

## TCP Delayed Acknowledgment

TCP delayed acknowledgment is an optimization technique to combine multiple acknowledgments (ACKs) into a single response to reduce the overhead. Upon receiving data, receivers (using delayed acknowledgment) don't send acknowledgment right away but instead wait for a few hundred milliseconds (200ms to 500ms) so it can be sent bundled together with any other acknowledgments or data that it might generate in during that window.

**Nagle's algorithm (sender side) and TCP delayed acknowledgement (receiver side) is a double whammy for real-time applications**: receivers wait a few hundred milliseconds before acknowledging senders. Without receiving acknowledgement, senders keep on buffering small packets until they receive acknowledgement or the buffered data exceeds maximum segment size, which can take a long time (> 200ms) if the data is small. This *double buffering* wreaks havoc on real-time applications and increases message latency. Nagle's algorithm would probably perform better without the TCP delayed acknowledgment, which can be disabled using the [TCP_QUICKACK](https://linux.die.net/man/7/tcp) socket option. However, it's not always possible to control behavior of client devices. Besides, disabling Nagle's algorithm on the server for real-time applications does the job.

Until next time!
