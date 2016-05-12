---
layout: post
title: 10 Java Features Many Developers Haven't Heard Of
comments: True
excerpt_separator: <!--more-->
---

Doing code reviews, it dawned on me that many developers aren't yet familiar with the Java language features that were introduced as far back as Java 7 and are still doing things the old fashioned way. While there's no harm in doing things the old fashioned way if it works, knowledge is power. In this post, I'm going to look at 10 features that many Java developers might not have heard of.

A word of warning: just because a feature exists doesn't mean you must use it. Do not rush and use a feature that you do not yet understand.

Without further ado, let's get started.

## 1. The try-with-resources Statement

Prior to Java 7, working with [`InputStream`](https://docs.oracle.com/javase/7/docs/api/java/io/InputStream.html) (and other similar API) produced ugly looking code. Here's an egregious example.

```java
InputStream is = new FileInputStream("unreadme.txt");
try {
    // Read the file
} catch(IOException e) {
   // Handle if an exception occurs while reading the file
} finally {
  try {
    if(stream != null) {
      stream.close();
    }
  } catch(IOException e) {
    // Handle if an exception occurs while closing the file
  }
}
```
There's a lot of noise in the code above and twin `try-catch` statements. It is unnecessarily verbose, even by Java standards.

The *try-with-resources* statement addresses this issue. In it, you declare any object that implements [`java.lang.Closeable`](https://docs.oracle.com/javase/8/docs/api/java/io/Closeable.html) at the start of the block. When the block exits, it automatically closes the Stream by calling its `close()` method. Let's rewrite code above using *try-with-resources*:

```java
try(FileInputStream is = new FileInputStream("unreadme.txt")) {
  // do something with the file
} catch(IOException e) {
  //...
}
```

<!--more-->

You can even specify multiple `Closeable` objects and they will all be automatically closed.

## 2. Catch Multiple Exceptions in a Single `catch` Block
Since Java 7, multiple exceptions can be caught in a single `catch` block which makes the code less verbose and avoids duplication. Here's an example of the multi-catch block:

```java
try {
  // Code that throws multiple exceptions
} catch (IndexOutOfBoundsException | IOException ex) {
  logger.error("err", ex);
}
```
Instead of the usual:

```java
try {
  // Code that throws multiple exceptions
} catch (IndexOutOfBoundsException oobe) {
  logger.error("trouble traversing", oobe);
} catch (IOException ioe) {
    logger.error("problem reading file", ioe);
}
```

Also, don't do this:

```java
try {
  // Code that throws multiple exceptions
} catch (Exception e) {
  logger.error("an error occurred", e);
}
```

Catching `Exception` is generally a bad idea. The block in the code above will catch all exceptions that are thrown in the code above including the ones that it cannot handle and prevent upper methods in the stack from handling it properly.

The only catch is that the exceptions in multi-catch must be disjoint. See this [SO answer](http://stackoverflow.com/questions/8393004/in-a-java-7-multicatch-block-what-is-the-type-of-the-caught-exception) for more details.

## 3. Underscores in Numeric Literals
Starting from Java 7, you can use underscores in numeric literals to make your code more readable. Examples:

```java
long phoneNumber = 123_456_7890L;
long data = 0B1101001_10000100_10011010;
int x6 = 0x5_2;
```
The underscores can appear **anywhere between the digits**, except at the start or at the end of literal.

## 4. Default Methods
Since Java 8, you can include **method bodies [to interfaces](https://docs.oracle.com/javase/tutorial/java/IandI/defaultmethods.html)** which wasn't allowed in the previous versions of Java. These methods are known as the **default methods** because they are **automatically** included in classes that implement the interface. Default methods were added for backwards compatibility reasons and you should [use them judiciously](https://zeroturnaround.com/rebellabs/how-your-addiction-to-java-8-default-methods-may-make-pandas-sad-and-your-teammates-angry/).

This blog has a good [overview of the subject](http://zeroturnaround.com/rebellabs/java-8-explained-default-methods/).

## 5. Parallel Sorting of Large Arrays
This one's my favorite. It allows **larger arrays to be sorted faster**. The way it works is by dividing the larger array into several smaller subarrays and then sorting each subarray concurrently on in parallel. The results are combined or merged back together to provide the answer. So instead of,

```java
Array.sort(someArray); // Sorts arrays sequentially
```

Starting with Java 8, you could use:

```java
Arrays.parallelSort(someArray); // Parallel Sorting
```

The analysis done for this [article](http://www.drdobbs.com/jvm/parallel-array-operations-in-java-8/240166287) of Dr. Dobb's got 4x better performance:

> I loaded the raw integer data from an image into an array, which ended up at **46,083,360 bytes in size** (this will vary depending on the image you use). The serial sort method took almost 3,000 milliseconds to sort the array on my quad-core laptop, while the parallel sort methods took a maximum of about 700 milliseconds. It's not often that a new language release **updates the performance of a class by a factor of 4x**.

4x!? I'd be very pleased with anything over 2x.

## 6. Optional Values
Java 8 has introduced a container object called [`Optional`](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html) for wrapping references that may not be present or `null`.

```java
public static Optional<User> getUser(String id) {
  // If user is not found
  return null;
}
```

And here's how to call the method:

```java
Optional<User> optional = getUser("jhal1");
if (optional.isPresent()) {
  User user = optional.get();
} else {
  // No user found
}
```

In case you are wondering, what's wrong with just returning the `null` value? Callers aren't always aware that method may return `null` and do not always check for it. This happens frequently and is one of the reasons why the  [`NullPointerException`](https://docs.oracle.com/javase/7/docs/api/java/lang/NullPointerException.html)s are so plentiful. So, [if you are tired of null pointer exceptions](http://www.oracle.com/technetwork/articles/java/java8-optional-2175753.html), use `Optional`.


## 7. Strings in switch statements
I almost forgot that Java has a `switch-case` statement. Prior to Java 7, `switch-case` statement only worked with integer types (except `long`) and `enums`. Java 7 introduced the ability to use a `String` object as the expression. Here's how it works:

```java
String car = getCar();

switch(car) {
  case "Corvette":
  //Handle Corvette
  break;
  case "AC Cobra":
  //Handle AC Cobra
  break;
  case "McLaren F1":
  //Handle McLaren F1
  break;
  default:
  //Handle "Car not Found" error
  break;
}
```

Instead of the more cluttered:

```java
if (car.equals("Corvette")) {
  //Handle Corvette
} else if (car.equals("AC Cobra")) {
  //Handle AC Cobra
} else if (car.equals("McLaren F1")) {
  //Handle McLaren F1
} else {
  //Handle "Car not Found" error
}
```

## 8. The Diamond Operator
Diamond operators were introduced to make the use of generics a little less verbose. Take a look at this example:

```java
Map<String, List<String>> aMap = new HashMap<String, List<String>>();
```
Lots of visual clutter. The parameter types are duplicated. Since Java 7, you can omit the type definitions on the right side of the assignment expression and simply provide empty diamond operator, `<>`. The above statement could be rewritten as:

```java
Map<String, List<String>> aMap = new HashMap<>();
```

When the compiler encounters the diamond operator (<>), it infers the generic type arguments from the context automatically.

## 9. Annotations Everywhere
Thanks to Java 8, annotations can be retrofitted [almost anywhere](http://docs.oracle.com/javase/tutorial/java/annotations/basics.html) in your code. Great, because that's just what we needed (that's a joke). I'm fine with annotations but I've seen some developers go overboard trying to do **too much magic** with annotations. Too much of annotations, just like too much of anything, are bad. You're probably better off not knowing that this feature even exists.

## 10. Varargs
[Varargs](http://docs.oracle.com/javase/1.5.0/docs/guide/language/varargs.html) are useful for passing an arbitrary number of parameters to a method. Such as [`String.format(String format, Object...args)`](http://docs.oracle.com/javase/8/docs/api/java/lang/String.html#format-java.lang.String-java.lang.Object...-). Joshua Bloch in [Effective Java](http://www.amazon.com/Effective-Java-2nd-Joshua-Bloch/dp/0321356683) recommends using varargs judiciously:

> varargs are effective in circumstances where you really do want a method with a variable number of arguments. Varargs were designed for printf, which was added to the platform in release 1.5, and for the core reflection facility (Item 53), which was retrofitted to take advantage of varargs in that release. Both printf and reflection benefit enormously from varargs. You can retrofit an existing method that takes an array as its final parameter to take a varargs parameter instead with no effect on existing clients. **But just because you can doesn’t mean that you should!**

That's sound advice. Just because there's a feature available, doesn't mean you have to use it.

#### Do you agree with the list? How many features in the list were new to you? Please let us know in the comments section below.
