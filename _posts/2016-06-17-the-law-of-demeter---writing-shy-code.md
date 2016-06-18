---
layout: post
title: The Law of Demeter - Writing Shy Code
comments: True
excerpt_separator: <!--more-->
---

In all my years of building server-side applications, I have come to believe that the single most important aspect that determines the long term success of these projects isn't the speed of algorithms or the fancy frameworks. *Nope*. It's the complexity of the code. **Unmanaged complexity has a profound effect on the maintainability of large projects**. Applications that are difficult to understand aren't amenable to refactoring and introducing new features become a slow and painful process. I've seen complicated systems where developers were petrified of making even small changes in the fear that it might break some other part in the system. Or the spaghetti code that is only understood by a single individual or a handful of developers who get a free pass on anything because the project will be doomed if they quit.

<!--more-->

There are many factors that result in complicated code. **One of the factors is [coupling](https://en.wikipedia.org/wiki/Coupling_(computer_programming)) between your application's modules which makes it very difficult to understand or change the code**. Let's walkthrough a trivial example. Suppose there's a server that allows users to connect. When users connect and authenticate, they are wrapped in a 'User' class:

```java
// Represents a user
class User {
  public final String username;
  public final int id;
  public final Socket socket;
}
```

If we want to send a message a user, we would do something like this:

```java
// Send "Hello." string to a User
void sayHello(User user) {
  Socket s = user.socket; // Get the user's socket
  OutputStream outputStream = s.getOutputStream();
  PrintWriter out = new PrintWriter(outputStream);
  out.println("Hello."); // send the message to the socket.
}
```

I'm sure that the flaw is obvious: the *User* class failed to encapsulate the *socket* object and leaked it to the world. If we want to switch to another communication mechanism other than 'Socket', we'll have to make changes everywhere it is used . However, the `sayHello(...)` method isn't entirely innocent. **It sinned in the manner it interacted with the *socket* object. Instead of asking the *user* object to send a message, it obtained access to an independent "third-party" object (*socket*) that it used to send directly**. The example might be contrived, but I have seen this pattern far too many times in "enterprise" applications. The good news is that **this kind of coupling can be easily detected. The technique to identify this behavior has a fancy name: [The Law of Demeter](http://www.ccs.neu.edu/research/demeter/papers/law-of-demeter/oopsla88-law-of-demeter.pdf)**. The "law" (the term itself is a misnomer. It's a technique or a guideline) can be [summarized](https://en.wikipedia.org/wiki/Law_of_Demeter) as:

>
- Each unit should have only **limited knowledge about other units**: only units "closely" related to the current unit.
- Each unit should only talk to its friends; **don't talk to strangers**.
- **Only talk to your immediate friends**.

In short, tight coupling between logically independent modules violates the Law of Demeter. Although, it is a side effect of poor encapsulation, **the law tells says that we shouldn't obtain access to third-party objects and manipulate them directly**. Here's a good [example](http://pmd.github.io/pmd-5.1.3/rules/java/coupling.html) to illustrate the Law of Demeter:


```java
public class Foo {
    /**
     * This example will result in two violations.
     */
    public void example(Bar b) {
        // this method call is ok, as b is a parameter of "example"
        C c = b.getC();

        // this method call is a violation, as we are using c, which we got from B.
        // We should ask b directly instead, e.g. "b.doItOnC();"
        c.doIt();

        // this is also a violation, just expressed differently as a method chain without temporary variables.
        b.getC().doIt();

        // a constructor call, not a method call.
        D d = new D();
        // this method call is ok, because we have create the new instance of D locally.
        d.doSomethingElse();
    }
}
```

Scroll up and look at the `sayHello(...)` method above and notice how it violates the Law of Demeter. Even though **the method itself is pretty much helpless, it helps us detect tight-coupling**. The real problem is that the *User* class failed to hide its internal details. Let's fix it:

```java
class User {
  public final String username;
  public final int id;
  // Make the field private to hide it from the world.
  private final Socket socket;

  // Implement (encapsulate) the functionality of message sending
  void sendMessage(String message) {
    OutputStream outputStream = socket.getOutputStream();
    PrintWriter out = new PrintWriter(outputStream);
    out.println(message);
  }
}
```
Now that's done. Let's fix the `sayHello(...)` method to stop relying on the *socket* object:

```java
// Doesn't violate the Law of Demeter anymore.
void sayHello(User user) {
  user.sendMessage("Hello.");
}
```

(Coupling) Problem solved. In their book [The Pragmatic Programmer](https://www.amazon.com/Pragmatic-Programmer-Journeyman-Master/dp/020161622X), Andrew and Dave call for writing **"shy" code that doesn't interact with too many things**.

I keep an eye out for the Law of Demeter violations when writing or reviewing code. However, this could be automated with [source code analyzers](http://pmd.github.io/). I haven't personally used it myself so take my advice with a grain of salt. I'll update this post if I use it myself.
