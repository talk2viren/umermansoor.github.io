---
layout: post
title: The Law of Demeter - Writing Shy Code
comments: True
excerpt_separator: <!--more-->
---

In all my years of building server-side applications, I have come to believe that the single most important aspect that determines the long term success of these projects isn't the speed of algorithms or the fancy frameworks. *Nope*. It's the complexity of the code. **Unmanaged complexity has a profound effect on the maintainability of large projects**. Applications that are difficult to understand aren't amenable to refactoring. Introducing **new features become a slow and painful process, increasing the crucial time to market**. I've seen complicated systems where developers were petrified of making even small changes in the fear that they might inadvertently break some other part. Or the spaghetti code that is only understood by a single individual or a handful of developers who get a free pass on anything because the project will be doomed if they quit.

<!--more-->

There are multiple factors that contribute to code complexity. **One important factor is the amount of [coupling](https://en.wikipedia.org/wiki/Coupling_(computer_programming)) or interdependencies between the application's modules**. Let's walkthrough a trivial example. Suppose there's a server that allows users to connect. When users connect and authenticate, they are wrapped in a 'User' class:

```java
// Represents a user
class User {
  public final String username;
  public final int id;
  public final Socket socket;

  public void disconnect() {
    socket.close();
  }

  // Other methods that operate on the socket.
}
```

If we want to send a message a user, we would do something like this:

```java
// Another class
class Message {
  // Send "Hello." string to a User
  public void sayHello(User user) {
    Socket s = user.socket; // Get the user's socket
    OutputStream outputStream = s.getOutputStream();
    PrintWriter out = new PrintWriter(outputStream);
    out.println("Hello."); // send the message to the socket.
  }
}
```

The *User* class has an obvious flaw: it failed to encapsulate the *socket* object and leaked it to the world. If we want to change this implementation in the future (e.g. use an asynchronous socket library), we'll have to make changes in many places.

However, the *sayHello(...)* method of the *Message* class isn't entirely innocent. **It sinned in the manner in which it interacted with the *socket* object**. It **obtained access to an independent "third-party" object** (*socket*) and used it directly. The example might be contrived, but I have seen this pattern far too many times in "real-world" applications. The good news is that **this can be easily detected using a technique with a fancy name: [The Law of Demeter](http://www.ccs.neu.edu/research/demeter/papers/law-of-demeter/oopsla88-law-of-demeter.pdf)**. The "law" (the term itself is a misnomer. It's rather a technique or a guideline) can be [summarized](https://en.wikipedia.org/wiki/Law_of_Demeter) as:

>
- Each unit should have only **limited knowledge about other units**: only units "closely" related to the current unit.
- Each unit should only talk to its friends; **don't talk to strangers**.
- **Only talk to your immediate friends**.
>
> The fundamental notion is that **a given object should assume as little as possible about the structure or properties of anything else** (including its subcomponents), in accordance with the principle of "information hiding".

In short, tight coupling between logically independent modules violates the Law of Demeter. Although, it is a side effect of poor encapsulation, **the law discourages direct access and use of third-party objects**. Here's a good [example](http://pmd.github.io/pmd-5.1.3/rules/java/coupling.html) to illustrate the Law of Demeter:

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

Now scroll back up and look at the `sayHello(...)` method. It violates the Law of Demeter by talking to a stranger: the *socket* object. Even though **the method itself is pretty much helpless, it helps us detect tight-coupling**. The problem starts with the *User* class that failed to hide its internal details. So let's fix it:

```java
class User {
  public final String username;
  public final int id;
  // Make the field private to hide it from the world.
  private final Socket socket;

  // Simplified implementation to encapsulate messaging functionality
  void sendMessage(String message) {
    OutputStream outputStream = socket.getOutputStream();
    PrintWriter out = new PrintWriter(outputStream);
    out.println(message);
  }
}
```
Now we can fix the `sayHello(...)` method to stop relying on the *socket* object:

```java
class Message {
  // Doesn't violate the Law of Demeter anymore.
  public void sayHello(User user) {
    user.sendMessage("Hello.");
  }
}
```

(Coupling) Problem solved. In their book [The Pragmatic Programmer](https://www.amazon.com/Pragmatic-Programmer-Journeyman-Master/dp/020161622X), [Andrew](https://twitter.com/pragmaticandy) and Dave suggest writing **"shy" code that doesn't interact with too many things**.

I keep an eye out for the Law of Demeter violations when writing or reviewing code and **refactor code to reduce unnecessary coupling where it makes sense**. This could be automated with [source code analyzers](http://pmd.github.io/). I haven't personally used it myself so take my advice with a grain of salt. I'll update this post if I use it myself.
