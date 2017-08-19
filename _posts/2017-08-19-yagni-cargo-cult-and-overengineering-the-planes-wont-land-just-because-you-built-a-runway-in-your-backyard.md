---
layout: post
title: Yagni, Cargo Cult and Overengineering - the Planes Won't Land Just Because You Built a Runway in Your Backyard
comments: True
excerpt_separator: <!--more-->
---

It was April. Year was probably was 2010. The cold, snowy winter was finally coming to an end and the spring was almost in the air. I was preparing for my **final exams**. The review lectures were going on for the **RDBMS course** that I was enrolled in at my [university](https://www.ucalgary.ca/utoday/issue/2013-12-23/undergraduate-viewbook-and-admission-package-receive-marcom-awards).

![uofc]({{ site.url }}/img/blogs/uofc_winter_campus-0006.jpg)

<!--more-->

Around the same time, I had started hearing and reading about the shinny, new technology that was going to change the way we use databases. The *NoSQL* movement was gaining momentum. I was reading blogs about how MongoDB is big time outperforming ancient, [non web scale](https://www.youtube.com/watch?v=b2F-DItXtZs) relational databases.

After the lecture, being a smart-ass that I'm, I asked my professor:

>
*Me:* So, between RDBMS and *NoSQL* databases, which one do you think is the best?
>
*Professor:* Well, it depends.
>
*Me:* Depends on what?
>
*Professor:* Depends on what you are trying to achieve. Both have their pros and cons. **You pick the right tool for the job**.
>
*Me:* But MySQL can't really scale.
>
*Professor:* How do you think we got this far? Send me an email and I'll send you some papers and practical uses of MySQL handling very high load.

SQL was hard for my brain, especially the joins. I loved NoSQL. Simple *key->value* model without any joins! Stone-aged RDBMS systems that were designed in 1960's were simply not enough to keep up with modern demands. I had lost all interest in RDBMS and predicted they'll just die off in the next few years.

I never emailed my professor.

---

It's 2012. [We're *redesigning* my employer's flagship product](https://codeahoy.com/2016/04/21/when-to-rewrite-from-scratch-autopsy-of-a-failed-software/). The first version was a **monolith** that used the boring **MySQL**. Spending too much time reading blogs and *Hacker News* comments section, we convinced ourselves that we need to go big and modern:

- Break monolith into service-oriented architecture, aka, the SOA.
- Replace MySQL with Cassandra (MySQL to Redis to Cassandra)

And we built it.

There was nothing wrong with the new system... except **one major flaw**. It was too complex for a small startup team to maintain. We had built a **Formula One** race car, that makes frequent pit-stops and requires very specialized maintenance, when we needed a **Toyota Corolla** that goes on for years and years on just the oil change.

---

Fast forward to 2017. It feels like almost all software developers I interview these days, have hands-on experience with microservices architecture and many have even actually used it in production.

A grey San Francisco afternoon. I'm conducting an on-site interview. Masters degree and 3 years of experience at a startup that looked like it didn't make it. I asked him to tell me about the system he built.

>
*Guy:* We built the system using the *microservices* architecture. We had lots of small services which made our life really easy...
>
*Me:* Go on...
>
*Guy:* Data was written to the BI system through a *Kafka* cluster. *Hadoop* and *MapReduce* system was built to process data for analytics. The system was super scalable.

I pressed him to tell me drawbacks of microservices architecture and problems it introduces. The guy tried to hand-wave his way through and was convinced, just like I was in 2010 about NoSQL databases, that there are absolutely no issues with microservices architecture.

I'm not saying microservices architecture is bad. It's beneficial, **but only to a few organizations** in the world. Organizations who have very complex systems that justify the operational burden, **the overhead**, it introduces. Martin Fowler, who coined the term microservices, warns us of the "[microservices premium](https://martinfowler.com/bliki/MicroservicePremium.html)":

> The fulcrum of whether or not to use microservices is the complexity of the system you're contemplating. **The microservices approach is all about handling a complex system, but in order to do so the approach introduces its own set of complexities**. When you use microservices you have to work on automated deployment, monitoring, dealing with failure, eventual consistency, and other factors that a distributed system introduces. There are well-known ways to cope with all this, but it's extra effort, and nobody I know in software development seems to have acres of free time.
>
So my primary guideline would be **don't even consider microservices unless you have a system that's too complex to manage as a monolith**. The majority of software systems should be built as a single monolithic application. Do pay attention to good modularity within that monolith, but don't try to separate it into separate services.

Martin's been generous. I believe that only very few, very large organizations really benefit from microservices. Netflix is one of such organizations. Their system grew too large and too complex to justify switching to microservices.

In almost all cases, you can't go wrong by building a monolith first. You break your architecture into services-oriented or... microservices when the benefits outweigh the complexity.

The guy also mentioned **Kafka**. System that handles [2 million writes a second](https://engineering.linkedin.com/kafka/benchmarking-apache-kafka-2-million-writes-second-three-cheap-machines) at LinkedIn:

>
*Me:* How much data do you stream to Kafka roughly?
>
*Guy:* We *could* stream gigabytes of logs...
>
*Me:* How much data are you streaming *right now*?
>
*Guy:* Not a whole lot right now because we only have 3 customers. But the system could scale up to support millions and millions of users. Also, with both Kafka and Hadoop clusters, we get fool-proof fault-tolerance
>
*Me:* How big is the team and company?
>
*Guy:* Overall, I guess there were (less than 10) people in the company. Engineering was about 5.

At this point, I was tempted to ask if he had ever heard of *[YAGNI](https://martinfowler.com/bliki/Yagni.html)*:

> Yagni ... stands for "You Aren't Gonna Need It". It is a mantra from Extreme Programming ... It's a statement that some capability we **presume our software needs in the future should not be built now because "you aren't gonna need it"**.

People sometimes have honest intentions and they don't introduce new tools, libraries, frameworks, just for the sake of *enhancing their resumes*. Sometimes they simply speculate enormous growth and try to do everything up front to not have to do this work later.

> The common reason why people build presumptive features is because they think it will be cheaper to build it now rather than build it later. But that cost comparison has to be made at least against the **cost of delay**, preferably factoring in the probability that you're building an unnecessary feature, for which your odds are at least ⅔.

---

People know complexity is bad. No one likes to see bugs filed on JIRA or get PagerDuty alerts at 3 a.m. that something is wrong with Cassandra cluster. But why do software developers still do it? Why do they choose to build complex systems without proper investigation?

Are there other reasons for building complex features besides preparing for a hypothetical future?

I'm sure majority of you are familiar with the term **cargo cult software development**. Teams who slavishly and blindly follow techniques of large companies they idolize like Google, Apple or Amazon, in the hopes that they'll achieve similar success by emulating their idols.

Just like the South Sea natives who built primitive runways, prayed and performed rituals in the hopes that planes would come in and **bring cargo**, the food and supplies. **Richard Feynman** warned graduates at the California Institute of Technology to not fall victim to the cargo cult thinking:

> In the South Seas there is a cargo cult of people. During the war they saw airplanes land with lots of good materials, and they want the same thing to happen now. So they’ve arranged to make things like runways, to put fires along the sides of the runways, to make a wooden hut for a man to sit in, with two wooden pieces on his head like headphones and bars of bamboo sticking  out like antennas — he’s the controller — and they wait for the airplanes to land. They’re doing everything right. The form is perfect. It looks exactly the way it looked before. But it doesn’t work. No airplanes landed.

![cargo-cult]({{ site.url }}/img/blogs/cargo-cult.jpeg)

It reminded me of the time when we took a perfectly good monolith and created service-oriented architecture (SOA). **It didn't work. No planes landed**.

Whatever the reasons:
- *not wanting to be left out*: the new Javascript framework from this week will be the next hottest thing.
- *enhancing resume with buzzwords*: Monolith won't impress recruiters / interviewers.
- *imitating heroes*: Facebook does it, Google does it, Twitter does it.
- technology that could keep up with the projected 5000% YoY company growth: YAGNI.
- latest tools to convince people that you're a *proper* SF bay area tech company.

Cargo-cult engineering just doesn't work. You are not Google. What worked for them, didn't work for a 9-person bay area startup. Google actually needed MapReduce because they wanted to regenerate indexes for the entire World Wide Web, or something like that. They needed fault tolerance from thousands of commodity servers. They had **[20 petabytes of data](https://www.usenix.org/legacy/event/osdi08/tech/full_papers/zaharia/zaharia_html/index.html)** to process.

20 petabytes is just enormous. In terms of the number of disk drives, here's what half of that, 10 petabytes, would [look](https://www.backblaze.com/blog/10-petabytes-visualized/) like:

![10petabytes]({{ site.url }}/img/blogs/10petabytes.jpg)

To avoid falling in the cargo-cult trap, I have learned to do the following:

- Focus on the problem first, not the solution. Don't pick any tool until you have fully understood what you are trying to achieve or solve. Don't give up solving the actual problem and make it all about learning and using the shinny new tech.
- Keep it simple. It's an over-used term, but software developers just don't get it. Keep. It. Simple.
- If you are leaning towards something that Twitter or Google uses, do your homework and understand the real reasons why they picked that technology.
- When thinking of growth, understand that the chances of your startup growing to be the size of Facebook are slim to none. Even if your odds are huge, is it really worth all this effort to set-up a 'world-class foundation' now vs doing it later?
- Weigh operational burden and complexity. Do you really need multi-region replication for fault-tolerance in return of making your DevOps life 2x more difficult?
- Be selfish. Do you want to be woken up in the middle of night because something, somewhere stopped working? There is nothing wrong with learning the new JavaScript framework. Create a new project and publish it on GitHub. Don't build production systems just because you like it.
- Think about people who'd have to live with your mess. Think about your legacy. Do people remember you as the guy who built rock-solid systems or someone who left a crazy mess behind?
- Share your ideas with experts and veterans and let them criticize. Identify people in other teams who you respect and who'd disagree freely.
- Don't jump to conclusions on the results of quick experiments. *HelloWorld* prototypes of anything is easy. Real-life is very different from *HelloWorld*.

---

We discussed YAGNI in this post, but it is generally applied in the context of writing software, design patterns, frameworks, ORMs, etc. Things that are in control of one person.

> YAGNI is coding what you need, as you need, refactoring your way through.

Back to the guy I interviewed. It's highly unlikely, even for a small startup, that a software developer in the trenches was allowed to pick Hadoop, Kafka and microservices architecture. Cargo cult practices usually start from someone higher up the ranks. A tech leader who may be a very smart engineer, but very bad at making rational decisions. Someone who probably spends way too much time reading blogs and tries very hard keep up with the Amazon or Google way of building software.

---

2017 is almost over. **NoSQL has matured**. MongoDB is on its way out. DynamoDB is actually a very solid product and is maturing really well. **RDBMS systems didn't die**. One can argue they are actually doing pretty good. StackOverflow is powered by *just* [4 Microsoft SQL Servers](https://nickcraver.com/blog/2016/02/17/stack-overflow-the-architecture-2016-edition/). [Uber runs on MySQL](https://eng.uber.com/mysql-migration/).

---

You may have a great reasons to use MapReduce or microservices. What matters is how you arrive at your decision. Did you fell victim to the hype-cycle and picked up the technology just to jump on the bandwagon? Or did you carefully analyze the problem, and picked the *best* tool that gets the job done and you and your team can deal with it?

As the professor said: "Pick the right tool for the job." I'll also add: don't build Formula One cars when you need a Corolla.
