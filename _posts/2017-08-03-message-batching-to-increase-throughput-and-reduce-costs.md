---
layout: post
title: Message Batching to Increase Throughput and Reduce Costs
comments: True
excerpt_separator: <!--more-->
---

A while ago, I was working on a backend system on the AWS cloud. Individual services in the system communicated by exchanging *asynchronous messages* with each other using [Amazon SQS](https://aws.amazon.com/sqs/). During early stages of development, we ran small load tests and found that CPU use was high and we would need more servers to handle the load. (Estimated peak load was 50,000 requests per second.) Services were generating lots of small messages every second and the profiler showed that threads responsible for handling and sending messages to SQS, **one message at a time**, were using CPU more than they should. This was affecting performance and throughput.

<!--more-->

To overcome this challenge, we took a page from Amazon's [best practices advice](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/throughput.html), and introduced **batching and buffering** on senders to group multiple messages and send as one batch. This simple fix **increased the throughput**, **cut down SQS costs** (SQS [pricing](https://aws.amazon.com/sqs/pricing/) is by *number* of messages), and had **very little impact on latency**. *Win, win, win*. The *custom* batching algorithm was simple: batch up to 15 messages or wait up to a maximum of 50 milliseconds. If either 15 messages arrive quickly to form a batch or 50 milliseconds elapse, the batch is sent out. These figures (batch size and maximum time) were established after trial and error, tuned for the best throughput and latency. The application was multi-threaded with hundreds of active threads, so the system allowed **multiple concurrent** batches to be active at the same time to reduce thread blocking.

The only downside is that if the application crashes after retrieving batch from the queue but **before processing** messages in it, we can **lose** some or all of the messages. To deal with complete message loss, the system can implement **retries** and resend messages upon timeout. Clients will see 2x response times, but at least get their response back.

If you are building applications which *generate* and exchange a **lot of messages** in **short periods**, batching can increase *throughput*, *performance* and in the case of SQS, *reduce costs*. I recommend using message batching for SQS even if only for cost reasons.

Until next time.
