---
layout: post
title: htop Explained Visually
comments: True
excerpt_separator: <!--more-->
---

[htop](http://hisham.hm/htop/) is a great tool for monitoring system resources and performance. It's a far superior alternative to [top](http://man7.org/linux/man-pages/man1/top.1.html) which comes preinstalled on Linux. I used htop many times to troubleshoot [ec2](https://aws.amazon.com/ec2/) instances especially when [CloudWatch](https://aws.amazon.com/cloudwatch/) was lagging behind.

![htop]({{ site.url }}/img/htop-small.png)

<!--more-->

htop is interactive and has a nice visual interface. It packs a lot of information which can be daunting to look at. I tried to find a nice infographic to explain what each number, value or color coded bars mean, but couldn't find any. I myself didn't understand everything that htop displays. Then I stumbled upon Pēteris Ņikiforovs's [excellent analysis of htop](https://peteris.rocks/blog/htop/) and was enlightened. I encourage you to read Pēteris's blog post for it not only explains htop, but is a fantastic insight into linux internals through the lens of htop.

On my way back from the Christmas break, I had some time to spare so I decided to create a quick infographic for myself, my team and for you dear readers. I didn't get a chance to finish it until today.

Here's what htop looks like when you first run it. (Image courtesy of [Joe Collins](https://www.youtube.com/channel/UCTfabOKD7Yty6sDF4POBVqA))

![htop]({{ site.url }}/img/htop.png)

I've broken the interface into two sections so I have room to annotate. We'll look at the top and the bottom sections separately.

Let's start at the top.

![htop]({{ site.url }}/img/htop-top.png)

Here's the bottom section of htop.

![htop]({{ site.url }}/img/htop-bottom.png)

That's all. I hope you found this post useful. Until next time.
