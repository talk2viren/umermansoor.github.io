---
layout: post
title: 10 Java Features Many Developers Haven't Heard Of
comments: True
excerpt_separator: <!--more-->
---

After doing code reviews recently, it dawned on me that many developers who **use Java casually or as a second language** aren't familiar or intimate with some cool improvements and features that were introduced in Java 7 or earlier. In this post, I'll list 10 features that I feel everyone programming in Java must at least know about. I'm excluding the [big](https://docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html) [changes](http://www.oracle.com/technetwork/articles/java/ma14-java-se-8-streams-2177646.html) in Java 8 since everyone seem to at least know about them.

<!--more-->

Here's a list of features covered in this post so you could skip to the one you don't know about or skip this post entirely if you know them all :-)

1. [The try-with-resources Statement](#1)
2. [Catch Multiple Exceptions in a Single `catch` Block](#2)
3. [Underscores in Numeric Literals](#3)
4. [Default Methods (in Interface)](#4)
5. [Parallel Sorting of Large Arrays](#5)
6. [Optional Return Values](#6)
7. [Strings in `switch` statements](#7)
8. [The Diamond Operator `<>`](#8)
9. [Annotations Everywhere](#9)
10. [Varargs](#10)

A word of caution: There's no harm in doing things the old fashioned way if it is working. It is much better than rushing to use a feature that is not fully understood. But knowledge is power.

Without further ado, let's get started.

## <a name="1"></a> 1. The try-with-resources Statement

Prior to Java 7, working with [`InputStream`](https://docs.oracle.com/javase/7/docs/api/java/io/InputStream.html) (and other similar APIs) produced ugly looking code. Here's an egregious example.

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

The *try-with-resources* statement addresses this issue. You declare any object that implements [`java.lang.Closeable`](https://docs.oracle.com/javase/8/docs/api/java/io/Closeable.html) at the start of the block. When the block exits, Java automatically closes the object by calling its `close()` method. Let's rewrite the above example using *try-with-resources*:

```java
try(FileInputStream is = new FileInputStream("unreadme.txt")) {
  // do something with the file
} catch(IOException e) {
  //...
}
```

Much cleaner. You can even specify multiple `Closeable` objects.

## <a name="2"></a> 2. Catch Multiple Exceptions in a Single `catch` Block
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

Catching [`Exception`](https://docs.oracle.com/javase/7/docs/api/java/lang/Exception.html) is generally a bad idea. The `catch` block in the example above will catch all exceptions that are thrown in the `try` body including the ones that it cannot handle. This prevents upper methods in the stack from handling the exception properly.

The only catch is that the exceptions in multi-catch must be disjoint. See this [Stack Overflow answer](http://stackoverflow.com/questions/8393004/in-a-java-7-multicatch-block-what-is-the-type-of-the-caught-exception) for more details.

## <a name="3"></a> 3. Underscores in Numeric Literals
Starting from Java 7, you can use underscores in numeric literals to make your code more readable. The underscores can appear **anywhere between the digits**, except at the start or at the end of literal. Here are some examples taken from [Oracle's tutorial](https://docs.oracle.com/javase/tutorial/java/nutsandbolts/datatypes.html):

```java
long creditCardNumber = 1234_5678_9012_3456L;
long socialSecurityNumber = 999_99_9999L;
long phoneNumber = 123_456_7890L;
long data = 0b11010010_01101001_10010100_10010010;
byte nybbles = 0b0010_0101;
long maxLong = 0x7fff_ffff_ffff_ffffL;
int x6 = 0x5_2;
```

Although, I'm not sure if it would ever make sense to store credit cards or social security numbers in your code, **using underscores certainly make reading hex and binary variables a lot more convenient.**


## <a name="4"></a> 4. Default Methods (in Interface)
Since Java 8, you can include **method bodies [to interfaces](https://docs.oracle.com/javase/tutorial/java/IandI/defaultmethods.html)** which wasn't allowed in the previous versions of Java. These methods are known as the **default methods** because they are **automatically** included in classes that implement the interface. Default methods were **added primarily for backwards compatibility reasons and you should [use them judiciously](https://zeroturnaround.com/rebellabs/how-your-addiction-to-java-8-default-methods-may-make-pandas-sad-and-your-teammates-angry/)**.

This blog has a good [overview of the subject](http://zeroturnaround.com/rebellabs/java-8-explained-default-methods/).

## <a name="5"></a>5. Parallel Sorting of Large Arrays
This one's my favorite. It allows larger arrays to be **sorted faster**. It works by dividing a large array into several smaller subarrays and then sorting each subarray concurrently or in parallel. The results are merged back together to form the answer. So instead of,

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

## <a name="6"></a>6. Optional Return Values
Java 8 has introduced a container object called [`Optional`](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html) for wrapping objects that may not be present or `null`. A method can wrap its return type in `Optional`. Let's look at an example:

```java
public static Optional<User> getUser(String id) {
  // If the user id is NOT FOUND, return null
  return null;
}
```

And here's how to call the method:

```java
Optional<User> optional = getUser("jhal1");
if (optional.isPresent()) {
  // User found. Get the value
  User user = optional.get();
} else {
  // No user found
}
```

In case you are wondering, what's wrong with just returning the `null` value? Callers aren't always aware that method may return `null` and do not always check for it. This happens frequently and is one of the reasons why the  [`NullPointerException`](https://docs.oracle.com/javase/7/docs/api/java/lang/NullPointerException.html)s are so plentiful. So, [if you are tired of null pointer exceptions](http://www.oracle.com/technetwork/articles/java/java8-optional-2175753.html), use `Optional`.

## <a name="7"></a>7. Strings in `switch` statements
I almost forgot that Java has a `switch-case` statement. Prior to Java 7, `switch-case` statement only worked with integer types (except `long`) and `enums`. Java 7 introduced the ability to use a `String` object as the expression. Here's how it looks:

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

Which is equivalent to the more cluttered:

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

## <a name="8"></a>8. The Diamond Operator `<>`
Diamond operators were introduced to make the use of generics a little less verbose. Take a look at this example:

```java
Map<String, List<String>> aMap = new HashMap<String, List<String>>();
```

The parameter types are duplicated on the left and right sides of the expression. Since Java 7, you can omit the type definitions on the right side of assignment expressions with a diamond operator, `<>`. The above statement could be rewritten as:

```java
Map<String, List<String>> aMap = new HashMap<>();
```

When the compiler encounters the diamond operator (<>), it **infers the generic type arguments from the context**.

## <a name="9"></a>9. Annotations Everywhere
Thanks to Java 8, annotations can be retrofitted [almost anywhere](http://docs.oracle.com/javase/tutorial/java/annotations/basics.html) in your code. Great, because that's [just what we needed](http://www.annotatiomania.com/). I'm fine with annotations, but I've seen some developers going overboard, trying to do **too much magic** with annotations. Too much of annotations, just like too much of anything, are bad. You're probably better off not knowing that this feature even exists. End of my rant.

## <a name="10"></a>10. Varargs
[Varargs](http://docs.oracle.com/javase/1.5.0/docs/guide/language/varargs.html) are useful for passing an arbitrary number of parameters to a method. Such as [`String.format(String format, Object...args)`](http://docs.oracle.com/javase/8/docs/api/java/lang/String.html#format-java.lang.String-java.lang.Object...-). Joshua Bloch in [Effective Java](http://www.amazon.com/Effective-Java-2nd-Joshua-Bloch/dp/0321356683) recommends using varargs judiciously:

> varargs are effective in circumstances where you really do want a method with a variable number of arguments. Varargs were designed for printf, which was added to the platform in release 1.5, and for the core reflection facility (Item 53), which was retrofitted to take advantage of varargs in that release. Both printf and reflection benefit enormously from varargs. You can retrofit an existing method that takes an array as its final parameter to take a varargs parameter instead with no effect on existing clients. **But just because you can doesn’t mean that you should!**

That's sound advice. Just because there's a feature available, doesn't mean you have to use it.
