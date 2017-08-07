---
layout: post
title: Basics of Java Garbage Collection
comments: True
excerpt_separator: <!--more-->
---

>Knock, knock.
>
Who's there?
>
*...long GC pause...*
>
Java.

![wiseguy-eh]({{ site.url }}/img/blogs/wiseguy.jpg)

It's an old joke when Java was *new* and slow compared to other languages. These days, [Java is fast](http://benchmarksgame.alioth.debian.org/) and powers many real-time applications serving hundreds of thousands of concurrent users. It's no news to anyone that the **biggest impact on Java's performance these days comes from its garbage collection**. Fortunately, almost in all cases, garbage collection can we **tweaked and optimized to boost performance**. Tuning garbage collector is a chore which requires knowledge of garbage collection and lots of patience to profile the application to understand its behavior. A few times a year, I find myself explaining garbage collection to a new developer who is not familiar or I'm troubleshooting garbage collection related performance issues myself. In this post, I'll visit this garbage collection the way I know it and explain few key concepts with examples. This post is for you if you want to understand garbage collection at a high-level and at the very least, want enough information to start your garbage collection performance tuning journey.


<!--more-->

Java ships with **several** garbage collection algorithms. Each works differently and has its own pros and cons. The most important thing to keep in mind is that all garbage collection algorithms **stop the world**. That is, your application is put on hold or paused, as the garbage is collected and taken out. The main difference among the algorithms is *how* they stop the world. Some algorithms sit completely idle until the garbage collection is absolutely needed and then pause your application for long period while others **work concurrently with your application** and thus need a **shorter pause** during stop the world phase. The best algorithm depends on your goals: are your **optimizing for throughput** where long pauses every now and then are tolerable or you are **optimizing for low latency** by spreading it out and having shorter pauses.

To *enhance* the garbage collection process, Java (HotSpot JVM, more accurately) divides up the heap memory into two main areas: **Young Generation** and **Old Generation** (also called Tenured). There is also a Permanent Generation, but we won't be discussing it.  

Young generation is where *young* objects live. It's further subdivided into the following areas:

1. Eden Space
2. Survivor Space 1
3. Survivor Space 2

By default, **Eden is bigger** than the two survivor spaces combined. On my Mac OS X, the 64-bit HotSpot, Eden takes about 76% of all the young generation space. All objects are first created in the 'Eden'. When Eden is full, a **minor** garbage collection is triggered. All new objects are quickly inspected to check their eligibility for garbage collection. The ones that are dead, that is, aren't referenced (ignoring reference strength for this discussion) from other objects are marked as dead and garbage collected. The **surviving objects are moved to one of the 'survivor spaces'**, whichever of the two is completely empty.

Subsequent minor garbage collections (triggered when Eden is full) repeat the same process and objects **ping-pong** from Eden and one of the survivor space (known as the 'from' survivor space) *to* the other (known as the 'to' survivor space) until one of the following happens:

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
-Xmx100m                     // Give JVM 100 MB of heap memory
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

**Not what we predicted**. We can see that this time, the **old generation received objects right after the first minor** garbage collection. We know that these objects are short-lived and tenuring threshold is set to 15 and this is the first collection. What happened is the following: the application created a large number of objects which filled up Eden space. Minor garbage collection ran and tried to collect garbage. However, most of these short-lived objects were **active** during the GC, i.e. were being referenced from a live thread and being processed. The young generation garbage collector had **no choice but to push these objects to the old generation**. This is bad because the objects that got pushed to the old generation were **forcibly aged** and can only be cleaned up by old generation's major garbage collection which usually takes more time.

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

This is in no way an exhaustive method of tuning garbage collection. I'm simply trying to demonstrate the steps involved. For real applications, optimum settings are found as a result of trial and error with different settings. For example, we could have also fixed the problem by increasing the total heap memory size.

## Garbage Collection Algorithms

As we discussed earlier, Java comes with several different garbage collection algorithms for young and old generations. Prior to Java 7, garbage collection **algorithms worked either for young generation or old, but not both**. In addition, you can only pair up compatible algorithms. For example, you cannot pair up 'Parallel Scavenge' young generation collector with 'CMS' old generation collector because they are not compatible. To make it easier for you, I was going to make an infographic to show which garbage collectors are compatible, however, luckily I searched first and found one created by JVM engineer, [Jon Masamitsu](https://blogs.oracle.com/jonthecollector/our-collectors).

![gc-collectors-pairing]({{ site.url }}/img/blogs/gc-collectors-pairing.jpg)

Here's a description of these algorithms also taken from Jon's blog. Please note that I have never seen anyone use 'Serial' collector. It's old; avoid using it unless you are sure that it is right choice.

>
1. "Serial" is a stop-the-world, copying collector which uses a single GC thread.
2. "Parallel Scavenge" is a stop-the-world, copying collector which uses multiple GC threads.
3. **"ParNew"** is a stop-the-world, copying collector which uses multiple GC threads. It differs from "Parallel Scavenge" in that it has enhancements that make it usable  with CMS. For example, "ParNew" does the synchronization needed so that it can run during the concurrent phases of CMS.
4. "Serial Old" is a stop-the-world, mark-sweep-compact collector that uses a single GC thread.
5. **"CMS"**  (Concurrent Mark Sweep) is a mostly concurrent, low-pause collector.
6. "Parallel Old" is a compacting collector that uses multiple GC threads.

I used **ParNew** and [**CMS**](https://docs.oracle.com/javase/9/gctuning/concurrent-mark-sweep-cms-collector.htm#JSGCT-GUID-FF8150AC-73D9-4780-91DD-148E63FA1BFF) for **server side applications** where response time and latency are bounded by SLA's and must be kept low. CMS generally does a very good job to concurrently marking objects and stops the world to do **major** GC when the old generation memory is 70% full (this is the default, it can be changed with `-XX:CMSInitiatingOccupancyFraction=70` argument)

Since java 8, the [CMS has been deprecated](https://community.oracle.com/thread/3648979) and [Garbage-First](http://docs.oracle.com/javase/7/docs/technotes/guides/vm/G1.html) aka **G1** collector should be used instead.

> The G1 collector is a server-style garbage collector, targeted for multi-processor machines with large memories. It meets garbage collection (GC) pause time goals with high probability, while achieving high throughput.

**G1**, unlike other garbage collectors, **works on both old and young generation**. I've not experienced G1 collector first-hand and developers in my team are still using CMS, so I can't say if it's better than CMS. A quick online search of benchmarks reveals that [CMS performs better](http://blog.novatec-gmbh.de/g1-action-better-cms/) [than G1](https://dzone.com/articles/g1-vs-cms-vs-parallel-gc). Not what I expected. If you'd like to try G1, it can be enabled with:

```
-XX:+UseG1GC
```

Hope you found this post useful. Until next time.
