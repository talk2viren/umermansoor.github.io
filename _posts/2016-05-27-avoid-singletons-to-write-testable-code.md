---
layout: post
title: Avoid Singletons to Write Testable Code
excerpt_separator: <!--more-->
---

Often times in software development, there is a need to share a single object of a class throughout the code base. For example, we might want to store all online users in one central registry or share a central queue amongst all producer and consumer objects. We want to:

- ensure that exactly *one* object of a class exists.
- provide a way to get that object.

<!--more-->

This is a **valid and a very common requirement** but is often equated and related to a design pattern called [Singleton](https://sourcemaking.com/design_patterns/singleton). While they provide a quick and easy solution, **singletons are considered bad because they make unit testing and debugging difficult**. [Brian Button](https://www.linkedin.com/in/brianbutton) has made some valid [arguments](https://blogs.msdn.microsoft.com/scottdensmore/2004/05/25/why-singletons-are-evil/) against singletons:

> [Singletons] provide a well-known point of access to some service in your application so that you don’t have to pass around a reference to that service. How is that different from a global variable? (remember, globals are bad, right???) What ends up happening is that the **dependencies in your design are hidden inside the code, and not visible by examining the interfaces of your classes and methods**. You have to inspect the code to understand exactly what other objects your class uses.

> One of the underlying properties that makes code testable is that it is loosely coupled to its surroundings. This property allows you to substitute alternate implementations for collaborators during testing to **achieve specific testing goals (think mock objects). Singletons tightly couple you to the exact type of the singleton object**, removing the opportunity to use polymorphism to substitute an alternative.

He's absolutely right. It's very difficult to write unit tests for code that uses singletons because it is generally tightly coupled with the singleton instance, which makes it hard to control the creation of singleton or mock it.

Recently I came across a project that made very liberal use of singletons. When I asked the developer about it, he said: "*I know singletons are bad. But I needed a single instance of objects in all these cases and had no choice but to use the singleton.*" Wrong. Having a single instance is a valid requirement, but **singletons are not the only solution**. A cleaner alternative would be to create objects in one place like the `main()` method and pass them to other classes that need them using their constructors. **This concept is called ['Dependency Injection'](https://en.wikipedia.org/wiki/Dependency_injection)**.

To illustrate these concepts, let's look at few example. Suppose we want to store all online users in a central registry and provide a way to find out whether a user is online or not by the username. If we use singletons, the code would look like:

```java
public class SomeClassUsingSingleton {

  public boolean isUserOnline(String username) {
    // Obtain singleton instance directly
    UserRegistry userRegistry = UserRegistry.getInstance();
    return userRegistry.get(username) != null ? true : false;
  }

}
```

Let's rewrite the above code to use dependency injection instead of singletons. In this example, `UserRegistry` object is created in the `main()` method and passed to the class via its constructor:

```java
public class SomeClassUsingDependencyInjection {
  private final UserRegistry userRegistry;

  // Creators of the class pass an instance of UserRegistry
  public SomeClass(UserRegistry userRegistry) {
    this.userRegistry = userRegistry;
  }

  public boolean isUserOnline(String username) {
    return userRegistry.get(username) != null ? true : false;
  }

}
```

Problem solved. However initializing all objects in the `main()` method and passing them where they are needed is a mechanical task and increases the number of arguments that declared in constructors. Dependency injection (DI) frameworks and containers like [Google's Guice](https://github.com/google/guice) or [Spring](http://docs.spring.io/autorepo/docs/spring/3.2.x/spring-framework-reference/html/beans.html) are designed to *automate* this task. **You just declare *dependencies* on objects where you need them and the framework automatically provides or *injects* them**. It's that simple. Let's rewrite the above example using Spring:

```java
public class SomeClassUsingSpringDI {

  @Autowire //Will be injected automatically by Spring
  private final UserRegistry userRegistry;

  public SomeClass() {
  }

  public boolean isUserOnline(String username) {
    return userRegistry.get(username) != null ? true : false;
  }

}
```

And the `UserRegistry` class is declared as a Spring component:

```java
@Component
public class UserRegistry {
  ...
}
```

We can also have multiple implementations of the `UserRegistry` such as one that uses an in-memory data structure to store users and another one that uses an external cache, and tell the DI framework to pick and inject the right one at run-time.

**To write testable code, we must separate object creation from the business logic** and singletons, by their nature, prevent this by providing a global and a `static` way of creating and obtaining an instance of their classes. They make it impossible to mock and isolate methods that depend on them for testing. The `new` operator is no different and same arguments apply. Dependency injection frameworks centralize object creation and separates it from the business logic making it possible to isolate methods and write good unit tests. Projects of all sizes can benefit from DI frameworks. I normally include a DI framework form the very start because it's tough to resist the singleton temptation half-way through the project when you really need it and don't have time to mess around setting up DI.
