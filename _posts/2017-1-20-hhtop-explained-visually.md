---
layout: post
title: htop Explained Visually
comments: True
excerpt_separator: <!--more-->
---

[htop](http://hisham.hm/htop/) is a very nice linux utility that comes very handy when monitoring systems and processes. It has a great visual interface and graphs that update themselves in real time. I used it a lot when I had to troubleshoot applications or systems. Even though I used it frequently, I didn't fully understand all the information that it showed (it can be daunting to look at). Then I stumbled upon Pēteris Ņikiforovs's [excellent analysis of htop](https://peteris.rocks/blog/htop/) and was enlightened. I encourage you to read this blog post for it is a fantastic insight into the linux internals through the lens of htop.

<!--more-->

On my way back from the Christmas break, I had some time to spare so I decided to create a quick infographic for myself, my team and for you dear readers. I didn't get a chance to finish it until today.

Here's what you see when you first run htop. (Image courtesy of [Joe Collins](https://www.youtube.com/channel/UCTfabOKD7Yty6sDF4POBVqA))

![htop]({{ site.url }}/img/htop.png)

I've broken the interface into two sections: the top and the bottom. We'll look at them separately.

Let's start at the top.

![htop]({{ site.url }}/img/htop-top.png)

Here's the bottom section of htop.

![htop]({{ site.url }}/img/htop-bottom.png)

Until next time.
