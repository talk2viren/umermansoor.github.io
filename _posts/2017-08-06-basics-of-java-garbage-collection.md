---
layout: post
title: Basics of Java Garbage Collection
comments: True
excerpt_separator: <!--more-->
---

>
- **Knock, knock**.
- Who's there?
- *...long GC pause...*
- **Java**.

![wiseguy-eh]({{ site.url }}/img/blogs/wiseguy.jpg)

It's an old joke from the time when Java was *new* and *slow* compared to other languages. Over time, Java became a lot [**faster**](http://benchmarksgame.alioth.debian.org/). Today it powers many real-time applications with hundreds of thousands of concurrent users. These days, the **biggest impact on Java's performance comes from its garbage collection**. Fortunately, in many cases, it can be tweaked and optimized to improve performance.

<!--more-->

For most applications, the default settings of the JVM work fine. But when you start noticing performance issues caused by garbage collection and giving more heap memory isn't possible, you need to tune and optimize the garbage collection. For most developers, it's a **chore**. It requires patience and a good knowledge of how garbage collection works and an understanding of application's behavior. This post is a high-level overview of Java's garbage collection with some examples of troubleshooting performance issues.

Let's get started.

Java ships with **several** garbage collectors. More specifically, these are different *algorithms* that run in their own *threads*. Each works differently and has pros and cons. The most important thing to keep in mind is that all garbage collectors **stop the world**. That is, your application is put on hold or paused, as the garbage is collected and taken out. The main difference among the algorithms is *how* they stop the world. Some algorithms sit completely **idle until the garbage collection is absolutely needed** and then pause your application for a long period while others **do most of their work concurrently** with your application and thus need a **shorter pause** during stop the world phase. The best algorithm depends on your goals: are your **optimizing for throughput** where long pauses every now and then are tolerable or you are **optimizing for low latency** by spreading it out and having short pauses all along.

To *enhance* the garbage collection process, Java (HotSpot JVM, more accurately) divides up the heap memory into two *generations*: **Young Generation** and **Old Generation** (also called Tenured). There is also a *Permanent Generation*, but we won't cover it in this post.

![hotspot-heap]({{ site.url }}/img/blogs/hotspot-heap.png)

**Young generation** is where *young* objects live. It's further subdivided into the following areas:

1. Eden Space
2. Survivor Space 1
3. Survivor Space 2

By default, **Eden is bigger** than the two survivor spaces combined. On my Mac OS X, the 64-bit HotSpot JVM, Eden takes about 76% of all the young generation space. All objects are first created here. When Eden is full, a **minor** garbage collection is triggered. All new objects are quickly inspected to check their eligibility for garbage collection. The ones that are dead, that is, aren't referenced (ignoring reference strength for this discussion) from other objects are marked as dead and garbage collected. The **surviving objects are moved to one of the empty 'survivor spaces'**. Which one of two survivor spaces? To answer this question, let's discuss survivor spaces.

The reason for having two survivor spaces is to avoid **memory fragmentation**. Imagine if there was just one survivor space. While you are at it, also imagine survivor space as a contiguous array of memory. When young generation GC runs through the array, it identifies dead objects for removal. This would leave holes in memory where objects previously lived and **compaction** will be needed. To avoid compaction, HotSpot JVM just copies all live objects from the survivor space to the other (empty) survivor space so that there are no holes or empty spaces. While we are discussing compaction, please note that old generation garbage collectors (with the exception of CMS) perform compaction on the heap memory to avoid memory fragmentation.

In short, minor garbage collections (triggered when Eden is full) **ping-pong** objects from Eden and one of the survivor space (known as the 'from' survivor space in logs) *to* the other (known as the 'to' survivor space). This happens until one of the following happens:

1. Objects reach *maximum tenuring threshold*, in other words, have ping-pong'ed enough times that they aren't young anymore,
2. There is no room in survivor space to receive newly birthed objects (We'll revisit this later.)

When this happens, objects are moved to the old generation. (There could be other conditions but I'm not aware of them.) Let's try to understand with a real example. Suppose we have the following application that creates a few 'long-lived objects' during initialization and creates many short-lived during its operation. (E.g. a web server that allocates short-lived objects for each incoming request.)

```java
private static void createFewLongLivedAndManyShortLivedObjects() {
        HashSet<Double> set = new HashSet<Double>();

        long l = 0;
        for (int i=0; i < 100; i++) {
            Double longLivedDouble = new Double(l++);
            set.add(longLivedDouble);  // add to Set so the objects continue living outside the scope
        }

        while(true) { // Keep creating short-lived objects. Extreme but illustrates the point
            Double shortLivedDouble = new Double(l++);
        }
}
```

Let's enable garbage collection logs and other settings using the following JVM command line arguments:

```
-Xmx100m                     // Allow JVM 100 MB of heap memory
-XX:-PrintGC                 // Enable GC Logs
-XX:+PrintHeapAtGC           // Enable GC logs
-XX:MaxTenuringThreshold=15  // Allow objects to live in the young space longer
-XX:+UseConcMarkSweepGC      // Ignore for now; covered later
-XX:+UseParNewGC             // Ignore for now; covered later
```

The application logs showing the state before and after garbage collection are as follows:

<pre>
Heap <b>before</b> GC invocations=5 (full 0):
 par new (<u>young</u>) generation total 30720K, used 28680K
  eden space 27328K,   <b>100%</b> used
  from space 3392K,   <b>39%</b> used
  to   space 3392K,   0% used
 concurrent mark-sweep (<u>old</u>) generation total 68288K, used <b>0K</b> <br/>
Heap <b>after</b> GC invocations=6 (full 0):
 par new generation (<u>young</u>) total 30720K, used 1751K
  eden space 27328K,   <b>0%</b> used
  from space 3392K,   <b>51%</b> used
  to   space 3392K,   0% used
 concurrent mark-sweep (<u>old</u>) generation total 68288K, used <b>0K</b>
</pre>

From the logs, we can see a few things. The first thing to notice is that there have been 5 minor garbage collections before this one (total of 6.) Eden was 100% used which triggered it. One of survivor space is 39% used and as such has some room available. After the garbage collection is over, we can see that Eden went back to 0% and survivor space increased to 59%. This means that **live objects from Eden and survivor space were moved to second survivor space** and dead one's were garbage collected. How can we tell that some dead objects were collected? We can see that Eden is much larger than survivor space (27328K vs 3392K) and since survivor space size only slightly increased, a large number of objects must have been collected. The **old generation space stayed completely empty** before and after the garbage collection (Recall that the *tenuring threshold* was set to 15.)

Let's try an experiment. Let's run an application that is only creating short-lived objects in multiple threads. Based on what we've discussed so far, **none of these objects should go to the old generation**; minor garbage collection should be able to clean them up.

```java
private static void createManyShortLivedObjects() {
        final int NUMBER_OF_THREADS = 100;
        final int NUMBER_OF_OBJECTS_EACH_TIME = 1000000;

        for (int i=0; i<NUMBER_OF_THREADS; i++) {
            new Thread(() -> {
                    while(true) {
                        for (int i=0; i<NUMBER_OF_OBJECTS_EACH_TIME; i++) {
                            Double shortLivedDouble = new Double(1.0d);
                        }
                        sleepMillis(1);
                    }
                }
            }).start();
        }
    }
}
```

For this example, I'm gave the JVM only **10 MB** of memory. Let's look at the GC logs.

<pre>
Heap <b>before</b> GC invocations=0 (full 0):
 par new (<u>young</u>) generation total 3072K, used 2751K
  eden space 2752K,  99% used
  from space 320K,   0% used
  to   space 320K,   0% used
 concurrent mark-sweep (<u>old</u>) generation total 6848K, used <b>0K</b> <br/>
Heap <b>after</b> GC invocations=1 (full 0):
 par new generation  (<u>young</u>)  total 3072K, used 318K
  eden space 2752K,   0% used
  from space 320K,  99% used
  to   space 320K,   0% used
 concurrent mark-sweep (<u>old</u>) generation total 6848K, used <b>76K</b>
</pre>

**Not what we predicted**. We can see that this time, the **old generation received objects right after the first minor** garbage collection. We know that these objects are short-lived and tenuring threshold is set to 15 and this is the first collection. What happened is the following: the application created a large number of objects which filled up Eden space. Minor garbage collection ran and tried to collect garbage. However, most of these short-lived objects were **active** during the GC, i.e. were being referenced from a live thread and being processed. The young generation garbage collector had **no choice but to push these objects to the old generation**. This is bad because the objects that got pushed to the old generation were **prematurely aged** and can only be cleaned up by old generation's major garbage collection which usually takes more time.

How to fix this? There are several ways. One theoretical way is to estimate the number of active short-lived objects and size the young generation appropriately. Let us make the following changes:

- Young Generation by default is 1/3 of the old generation size. Let's change this using the -`XX:NewRatio=1` which gives young generation more memory (~3.4 MB compared to the 3.0 MB the last time.)
- Also increase the survivor space ratio using the `-XX:SurvivorRatio=1` argument. (~1.6MB each compared to 0.3 MB the last time.)

The problem was fixed. After 8 minor garbage collections, the old generation space was still empty.

<pre>
Heap <b>before</b> GC invocations=7 (full 0):
 par new generation   total 3456K, used 2352K
  eden space 1792K,  99% used
  from space 1664K,  33% used
  to   space 1664K,   0% used
 concurrent mark-sweep generation total 5120K, used <b>0K</b> <br/>
Heap <b>after</b> GC invocations=8 (full 0):
 par new generation   total 3456K, used 560K
  eden space 1792K,   0% used
  from space 1664K,  33% used
  to   space 1664K,   0% used [
 concurrent mark-sweep generation total 5120K, used <b>0K</b>
</pre>

This is in no way an exhaustive method of tuning garbage collection. I'm simply trying to demonstrate the steps involved. For real applications, optimum settings are found as a result of trial and error with different settings. For example, we could have also fixed the problem by doubling the total heap memory size.

## Garbage Collection Algorithms

As we discussed earlier, Java comes with several different garbage collection algorithms for young and old generations. At a high level, there are three general types of collectors available each with its own [performance characteristic](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/collectors.html):

> **serial collector** uses a single thread to perform all garbage collection work, which makes it relatively efficient because there is no communication overhead between threads. It is best-suited to single processor machines -XX:+UseSerialGC.
>
> **parallel collector** (also known as the throughput collector) performs minor collections in parallel, which can significantly reduce garbage collection overhead. It is intended for applications with medium-sized to large-sized data sets that are run on multiprocessor or multithreaded hardware.
>
> **concurrent collector** performs most of its work concurrently (for example, while the application is still running) to keep garbage collection pauses short. It is designed for applications with medium-sized to large-sized data sets in which response time is more important than overall throughput because the techniques used to minimize pauses can reduce application performance.

![gc-compared]({{ site.url }}/img/blogs/gc-compared.png)

HotSpot JVM allows you to configure different GC algorithms for young and old generation. You can only pair up compatible algorithms. For example, you cannot pair up *Parallel Scavenge* for young generation collector with *Concurrent Mark Sweep* for old generation collector because they are not compatible. To make it easier for you, I was going to make an infographic to show which garbage collectors are compatible, however, luckily I searched first and found one created by JVM engineer, [Jon Masamitsu](https://blogs.oracle.com/jonthecollector/our-collectors).

![gc-collectors-pairing]({{ site.url }}/img/blogs/gc-collectors-pairing.jpg)

>
1. "Serial" is a stop-the-world, copying collector which uses a single GC thread.
2. **"Parallel Scavenge"** is a stop-the-world, copying collector which uses multiple GC threads.
3. **"ParNew"** is a stop-the-world, copying collector which uses multiple GC threads. It differs from "Parallel Scavenge" in that it has enhancements that make it usable  with CMS. For example, "ParNew" does the synchronization needed so that it can run during the concurrent phases of CMS.
4. "Serial Old" is a stop-the-world, mark-sweep-compact collector that uses a single GC thread.
5. **"CMS"**  (Concurrent Mark Sweep) is a mostly concurrent, low-pause collector.
6. **"Parallel Old"** is a compacting collector that uses multiple GC threads.

I have been using **ParNew** (young generation) with [**Concurrent Mark Sweep**](https://docs.oracle.com/javase/9/gctuning/concurrent-mark-sweep-cms-collector.htm#JSGCT-GUID-FF8150AC-73D9-4780-91DD-148E63FA1BFF) (old generation) or the **Parallel collectors** for server side applications where response time and latency are bounded by SLA's and must be kept low. CMS does a good job of concurrently marking objects and has shorter major collection pauses. Major GC is triggered when the old generation memory is 70% full (this is the default, it can be changed with `-XX:CMSInitiatingOccupancyFraction=70` argument)

Concurrent Mark Sweep (*CMS*), paired with *ParNew*, works really well for server-side applications processing live requests from clients. I have been using it with ~ 10GB of heap memory and it keep response times steady and spikes aren't seen. Some developers I know use Parallel collectors (Parallel Scavenge + Parallel Old) are happy with results.

One important thing to know about the CMS is that there have been [calls to deprecate](http://openjdk.java.net/jeps/291) it and it will probably be deprecated in Java 9. Oracle recommends that the new concurrent collector, the [Garbage-First](http://docs.oracle.com/javase/7/docs/technotes/guides/vm/G1.html) or the **G1**, introduced first with Java, be used instead:

> The G1 collector is a server-style garbage collector, targeted for multi-processor machines with large memories. It meets garbage collection (GC) pause time goals with high probability, while achieving high throughput.

**G1** works on both old and young generation. It is optimized for larger heap sizes (~10 GB). I've not experienced G1 collector first-hand and developers in my team are still using CMS, so I can't yet compare the two. A quick online search of benchmarks reveals that [CMS performs better](http://blog.novatec-gmbh.de/g1-action-better-cms/) [than G1](https://dzone.com/articles/g1-vs-cms-vs-parallel-gc). I'd tread carefully. If you'd like to try G1, it can be enabled with:

```
-XX:+UseG1GC
```

Hope you found this post useful. Until next time.
