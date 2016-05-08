---
layout: post
title: Minimum Viable Product - Lessons for Software Teams
comments: True
excerpt_separator: <!--more-->
---

The concept of the minimum viable product or the MVP was popularized by Eric Reis in his book [The Lean Startup](http://www.amazon.com/Lean-Startup-Entrepreneurs-Continuous-Innovation/dp/0307887898). He defines it as:

> The minimum viable product is that version of a new product which allows a team to collect the maximum amount of validated learning about customers with the least effort.

Startups fail for many reasons but one of the **biggest reasons is that they build products that their customers do not want**. By that time, it's too late. They have already spent months or even years of their lives building a great product - a grand vision - that the market isn't, unfortunately, willing to purchase. MVP is a strategy to avoid exactly that scenario: **building something that the customers do not want**.

Eric [cites](http://www.inc.com/lee-clifford-julie-schlosser/lean-startup-eric-ries-testing-your-product.html) the story of Zappos. They didn't start out with a grand vision of building a cool website, distribution and call centers. Nope. **Zappos started by testing a simple hypothesis: are customers willing to buy shoes online?**

> **They went to local shoe store, took pictures of each of their products and put them online. If anyone bought shoes from them [at this early stage], they planned to go to the store, buy the shoes and mail them to the customer**. There was no big business behind it; there was a website and a hope that they'll get so many orders that it will get annoying to do all the purchasing and shipping manually. **It was all to test their big idea**.

<!--more-->

MVP is a great way to test the actual usage and assumptions as opposed to conventional market research that includes researching online, surveys etc. which often provide misleading results.

**Software teams can learn a lot from MVP and apply it to build products that that match their client needs**. Concepts like incremental, iterative development and continuos delivery have been around for quite some time, but none of these concepts capture the spirit of MVP. Martin Fowler defines [Continuous Delivery](http://martinfowler.com/bliki/ContinuousDelivery.html) as:

> Continuous Delivery is a software development discipline where you build software in such a way that the software **can be released** to production at any time.

MVP isn't about *"can be released"*. MVP says that the product **'must be released'**. And in order to release something, you must make it useful to your customers. This means capturing and implementing a small subset of essential features that deliver value to your customers so they can provide their feedback.

Usual software development goes like this: software teams talk to their clients and stakeholders to get their requirements (*Everyone does that. Right?*). They create a product vision (or write functional specifications) and show it to the stakeholders. Once the stakeholders agree on the requirements and the product vision, the team set out to build the product. The product is divided into several layers or several subs-systems, if following SOA or microservices architecture. Each layer is built piece-by-piece, incrementally and iteratively. When building these pieces, several micro-design questions popup which require feedback. Finally, once the pieces are ready, the product is released to the customer. The client **always flips and ask for changes or new features**. Depending on how well the team captured and modeled their requirements, the rework could take weeks or months!

The problem with the above approach is that even though the developers follow iterative development, they apply it to each piece in isolation as opposed to the entire product. John Mayo-Smith [contrasts](http://www.informationweek.com/two-ways-to-build-a-pyramid/d/d-id/1012280?) it with building a pyramid layer by layer.

![Flat Pyramid]({{ site.url }}/img/flat_pyramid.gif)

In contrast, MVP calls for delivering a "smaller product" that captures essential features that the customer could actually use and deliver feedback on. It's like building a small pyramid, showing it to the pharaoh, or the client, and evolving or growing it over time.

![Growing Pyramid]({{ site.url }}/img/growing_pyramid.gif)

I made this mistake when I architected a telecommunications project using Service Oriented Architecture (SOA). While the team followed iterative approach to individual services and did continuos integration, **we didn't implement essential requirements, the front-end per se, that our customer could use and provide feedback on**. The iterative approach worked great for the database layer, the business layer, the sniffers, the messaging layers and the load balancers. When the product was complete, we released it to our big client for a pilot with few thousand users. That same week, I received a phone call from the CEO asking for very different functionality, in an apologetic tone. It turned out that once the client used the product, they changed their mind. The big release date was one or two months away and we had to do major rework to adopt the change.

What can we learn from this story? Pick the base set of essential functionality that your clients can play with and do rest of the plumbing later. **Prototypes only help so much**. In the example I gave, the product's goal was to support mobile menus that allowed people to send messages and transfer funds. We prototyped and mocked the menus in [Balsamiq](https://balsamiq.com/) and even went as far as creating a simple HTML page that allowed the client to interact with the menu options. But **prototypes aren't as remotely interesting as an actual product. Clients looked at it and said that it looked fine. But once they got the actual product working on their phones, they got creative** with new ideas and ways to do things differently. If I was doing the project all over again, the first thing I'd do is start by building a set of menus, the front-end, that actually work on the real devices and show it to the client. I'd forget all about sexy things like distributed processing and the NoSQL databases - these pieces will come later and the product will grow over time.

Recently, I was working on a backend with REST based microservices architecture. There were several clients with different needs and there was a lot of unknown. A common complaint from the developers who worked on earlier projects was that **the clients always change their minds after the release which makes their products messy since they accrue [technical debt](http://codeahoy.com/2016/04/27/do-not-let-technical-debt-get-out-of-control/)**.
I had a Déjà vu. So we figured out the most urgent needs and defined a milestone that would deliver a working product. It'll have fewer services than our grand vision, but everything will work end-to-end. It won't be perfect or complete, but it will have just the right functionality they need to get up and running and provide us the feedback. And we will grow and evolve that product over time.

The closest reference to MVP I could find in software development is a concept called 'Tracer Bullets' that was first used by Andy and Dave in their book the [The Pragmatic Programmer](http://www.amazon.com/Pragmatic-Programmer-Journeyman-Master/dp/020161622X):

> We once undertook a complex client-server database marketing project ... The servers were a
range of relational and specialized databases. The client GUI, written in Object Pascal,
used a set of C libraries to provide an interface to the servers ... **There were many unknowns and many different environments**, and no one was too sure how the GUI should behave.
>
This was a great opportunity to use tracer code. We developed the framework for the front
end, libraries for representing the queries, and a structure for converting a stored query into
a database-specific query. Then we put it all together and checked that it worked. For that
initial build, all we could do was submit a query that listed all the rows in a table, but it
proved that the UI could talk to the libraries, the libraries could serialize and unserialize a
query, and the server could generate SQL from the result. **Over the following months we
gradually fleshed out this basic structure, adding new functionality by augmenting each
component of the tracer code in parallel**.

Build MVPs (or use Tracer Bullets) to guide your software development to **build the right thing**. Releasing a MVP often requires support from several other departments (DevOps, Marketing, Product Managers etc.) but it's well worth it. You might have seen this image a thousand times already, but this is exactly what MVP will avoid:

![Software Requirements]({{ site.url }}/img/software-requirements.png)
