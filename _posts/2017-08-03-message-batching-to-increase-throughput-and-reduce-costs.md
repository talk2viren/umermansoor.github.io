---
layout: post
title: Message Batching to Increase Throughput and Reduce Costs
comments: True
excerpt_separator: <!--more-->
---

A while ago, I was working on a backend system on the AWS cloud. Individual services in the system communicated by exchanging *asynchronous messages* with each other using [Amazon SQS](https://aws.amazon.com/sqs/). During early stages of development, we ran small load tests and found that CPU use was high and we would need more servers to handle the load. (Estimated peak load was 50,000 requests per second.) Services were generating lots of small messages every second and the profiler showed that threads responsible for handling and sending messages to SQS (one message at a time), were using CPU more than they should. This was affecting performance and throughput.

<!--more-->


To overcome this challenge, we took a page from Amazon's [best practices advice](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/throughput.html), and introduced **batching and buffering** on senders to group multiple messages and send as one batch. This simple fix **increased the throughput**, **cut down SQS costs** ([pricing](https://aws.amazon.com/sqs/pricing/) is by number of messages), and had **very little impact on latency**. *Win, win, win*. The *custom* batching algorithm was simple: batch up to 15 messages or wait up to a maximum of 50 milliseconds. If either 15 messages arrive quickly to form a batch or 50 milliseconds elapse, the batch is sent out. These figures (batch size and maximum time) were established after trial and error, tuned for the best throughput and latency. Because the application was concurrent with hundreds of active threads, the system allowed multiple concurrent batches to be active at the same time to reduce thread blocking.

Before I finish this post, let me highlight a drawback of message batching that comes to my mind. If an application **crashes** after successfully removing a batch from the queue but **before processing** messages, all or some messages can get **lost**. This may be fine if the system is designed to deal with some message loss. In our case, clients retried requests after timeout so the system could tolerate application crashes.

In a nutshell, if you are building applications which exchange a lot of messages with each other, message batching can increase throughput and performance and reduce costs.
