---
layout: post
title: Performance Testing Serverside Applications
comments: True
excerpt_separator: <!--more-->
---

Performance testing server-side applications is a crucial process to help understand *how the application behaves under load*. It helps software teams fine-tune their applications to get the best performance while keeping the infrastructure costs low. Performance testing answers several important questions such as:

* Is the application ready to handle the traffic that's going to hit it?
* What do average *response times* and *latencies* look like under normal and peak loads?
* Can the application be *scaled out*?
* What are the bottlenecks? (could be CPU, memory, an external service or a database server)
* How many instances are needed for supporting the estimated traffic (i.e. max RPS)?
* What type of instances are needed? Does the application requires an instance with higher CPU to Memory ratio? Or does it need an instance type that support high network utilization?
* Does the application slowly degrade in performance under load? Is it slowly leaking a resource that eventually crashes it after a few hours or days?

<!--more-->

![Leaky Pipe]({{ site.url }}/img/loaded-truck.jpeg)
*How much load can your application sustain before it buckles under?*

A lesson I learned recently is that **performance testing should not be an after-thought**. Software teams should start performance testing early in the release cycle and not wait until the end to do it. I once worked on a team that built a backend service that passed all unit, integration and end-to-end tests with flying colors. QA engineers didn't find any bugs in the application's logic. However, the *performance was just terrible when we ran load tests on it*. On a single [m4.large](https://aws.amazon.com/ec2/instance-types/) instance, the application supported 80% fewer requests than the team had estimated! This was a huge setback. The *main bottleneck* was found to be the 2-core CPU that was utilized to its maximum capacity as the application issued several queries to the database and applied complex algorithms to build a graph. Investigations by developers revealed that in order to reduce the amount of work the CPU was doing, it would require major design changes. But it was already too late - the release deadline was just weeks away. We decided to proceed with the release - albeit by over-provisioning the hardware and over-running our cost estimates by a factor of 3. All this could have been avoided if we had done performance testing sooner.

Performance testing is a broad topic. Teams I work with typically run [load](https://en.wikipedia.org/wiki/Load_testing) and [soak](https://en.wikipedia.org/wiki/Soak_testing) tests to measure performance metrics such as throughput, latency, resource utilization etc. using a wide variety of tools. At [Glu](www.glu.com), we build REST services in Java and use the following tools for our performance tests:

* [YourKit](https://yourkit.com/) Profiler to profile CPU and memory usage at a fine-grained level.
* [Apache Jmeter](http://jmeter.apache.org/) to generate load (in reality, Blazemeter or distributed Jmeter)
* [Amazon Cloudwatch](https://aws.amazon.com/cloudwatch/) to monitor resource utilization because we deploy our services on the Amazon cloud.
* [Hosted Graphite](https://www.hostedgraphite.com/) to observe custom metrics that the application generates and we are interested in.
* [Kibana](https://www.elastic.co/products/kibana) dashboards to look at the logs, errors, etc.

Before I wrap this post up, there are few other important lessons I'd like to share:

* **Run performance tests on a production-like environment**. I have seen teams run performance tests on their MacBooks Pros with 8-core CPU's and get [drastically different results](https://forums.aws.amazon.com/thread.jspa?threadID=16912) than from the actual cloud instances with puny, virtualized hardware.
* Create a good load test plan which requires careful thought. **The goal is to emulate the load that real users would generate** otherwise you might spend a lot of time chasing ghosts and fixing issues that are unlikely to happen in production. For example, I was investigating an issue with the performance of a Chat service under load. After looking at the load test script, I found that it grouped tens of thousands of users together. In reality, a group has on average about 10-50 people. The fix was to update the load test script to use a random group-id or a group-id from a pool instead of reusing the same id for each request.

I hope this post was helpful. Would love to hear your thoughts, the tools and the approach you take for performance testing. Till next time.
