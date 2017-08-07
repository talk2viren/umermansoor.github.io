---
layout: post
title: At Last, Amazon adds DynamoDB Auto Scaling
comments: True
excerpt_separator: <!--more-->
---

Earlier this month, Amazon [added](https://aws.amazon.com/about-aws/whats-new/2017/06/announcing-amazon-dynamodb-auto-scaling/) a long-requested feature with the introduction of **auto scaling** to DynamoDB. This allows read and write capacities of DynamoDB (of tables and global secondary indexes more accurately) to be adjusted automatically based on the demand. If you haven't used DynamoDB before, you might be wondering why is this important? One reason: **cost**. DynamoDB charges you by how much you provision. You end up paying the **full cost** by provisioned amounts, even if you **use less**. In other words, if you **overprovision** (e.g. for peak load), you'll pay extra during the non-peak hours, when it is used less. On the other hand, if you **underprovision** (e.g. for average load), the performance of your application will suffer due to **throttling** when load exceeds the provisioned capacity.

<!--more-->

Here's what it looks like.

![dynamodb-autoscale]({{ site.url }}/img/blogs/dynamodb-autoscale.png){:height="253px" width="700px"}

In the image above, I'm specifying that the table utilization is kept at 80%. If the utilization exceeds this threshold, auto scaling will increase the provisioned read capacity. Likewise, if the utilization falls below 80%, it will decrease the capacity to reduce unnecessary costs.

Previously, people had to make do with custom or third-party solutions to auto scaling. [Dynamic DynamoDB](https://github.com/sebdah/dynamic-dynamodb) was arguably the most popular. It's open source and has additional features such as time based auto scaling and [granular](https://dynamic-dynamodb.readthedocs.io/en/latest/granular_scaling.html) control over how many read and write capacity units to add or remove during auto scale up or down. However, now that this feature is built-in to DynamoDB and enabled by default, I doubt that third-party solutions will be used very much, if at all. *So long and thanks for all the fish*.

[Jeff Barr](https://twitter.com/jeffbarr) wrote a [blog post](https://aws.amazon.com/blogs/aws/new-auto-scaling-for-amazon-dynamodb/) introducing the new feature and has screenshots of CloudWatch graphs. Check it out if you want to learn more. One key takeaway from his blog is that auto scaling isn't ideal for **bursts**:

> DynamoDB Auto Scaling is designed to accommodate request rates that vary in a somewhat predictable, generally periodic fashion. If you need to accommodate unpredictable bursts of read activity, you should use Auto Scaling in combination with [DAX](https://aws.amazon.com/dynamodb/dax/) (read [Amazon DynamoDB Accelerator (DAX) – In-Memory Caching for Read-Intensive Workloads](https://aws.amazon.com/blogs/aws/amazon-dynamodb-accelerator-dax-in-memory-caching-for-read-intensive-workloads/) to learn more).

Until next time, ciao.
