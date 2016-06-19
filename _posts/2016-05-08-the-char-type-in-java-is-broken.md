---
layout: post
title: The char Type in Java is Broken
comments: True
excerpt_separator: <!--more-->
---

If I may be so brash, it is my opinion that the `char` type in Java is broken and should be avoided. It's not suitable for modern needs, makes debugging a nightmare and **should be retired**. `char` is for representing characters (e.g. 'a', 'b', 'c') and has been supported in Java since it was released about 20 years ago. When Java first came out, the world was a simpler place. Windows 95 was the latest, greatest operating system, world's [first flip phone](https://en.wikipedia.org/wiki/Motorola_StarTAC) was just put on sale, and Unicode had less than *40,000* characters, all of which fit perfectly into the 16-bit space that `char` provides. But things have changed drastically. Unicode has outgrown the 16-bit space and now requires 21 bits for all of its *120,737* characters.

<!--more-->

Java has supported Unicode since its first release and **strings are internally represented using [UTF-16](https://en.wikipedia.org/wiki/UTF-16) encoding**. UTF-16 is a *variable length* encoding scheme. For characters that can fit into the 16 bits space, it uses 2 bytes to represent them. For all other characters, it uses 4 bytes. This is great. All possible Unicode characters in existence plus a lot more (1 million more) could be represented using UTF-16 and thus as Strings in Java.

But `char` is a different story altogether. Let's look at its definition from the [official source](https://docs.oracle.com/javase/tutorial/java/nutsandbolts/datatypes.html):

> char: The char data type is a single **16-bit Unicode character**. It has a minimum value of '\u0000' (or 0) and a maximum value of '\uffff' (or 65,535 inclusive).

*"16-bit Unicode character"?* I guess [Joel](http://www.joelonsoftware.com/articles/Unicode.html) was right:

>  **Some people are under the misconception that Unicode is simply a 16-bit code where each character takes 16 bits and therefore there are 65,536 possible characters. This is not, actually, correct**. It is the single most common myth about Unicode, so if you thought that, don't feel bad.

There is no such thing as "16-bit Unicode character". Please read [Joel's article](http://www.joelonsoftware.com/articles/Unicode.html) if you don't understand the last statement.

`char` uses 16 bits to store Unicode characters that fall in the 0 - 65,535 which isn't enough to store all Unicode characters anymore. You might think: *Gee, 65,535 is plenty already. I'll never use that many*. That's true. But your users will. And when they send you a character that requires more than 16 bits, like these emojis 👦👩, **the `char` methods like `someString.charAt(0)` or `someString.substring(0,1)` will break and give you only half the code point. And the worst part is that the compiler won't even complain**. Recently, a fellow developer told me that their "North American users" started complaining that the chat nicknames and messages "aren't displaying properly". After a lot of grief, they found the issue and had to undo all `char` manipulation in their software to handle emojis and other cool characters. (Use [`codePointAt(index)`](https://docs.oracle.com/javase/7/docs/api/java/lang/String.html#codePointAt(int)) instead which returns an int that will fit all Unicode characters in existence.)

I have heard people say things like: *"if internationalization isn't a concern, you'd probably be fine using `char`"* or  *"don't worry about it unless your program is going to be released in China or Japan"*. This statement is wrong on two levels. First, at least I rarely come across applications where internationalization isn't a concern anymore. Second, **emojis characters are supported by all popular applications these days**. Unicode isn't just about internationalization anymore.

The `char` data type is broken. It might work for small or test applications, but if you start depending on it for string and character manipulation, it will disappoint. The application will 'break' silently and your users will see garbled text. May be, a UTF-16 character type from Oracle is the answer. Or at least a runtime exception when compiler detects that something bad is about to happen in the interim. Until then, we should probably avoid the `char` type. Even its official [JavaDocs](https://docs.oracle.com/javase/7/docs/api/java/lang/Character.html) don't sound all that convincing:

> The char data type (and therefore the value that a Character object encapsulates) are based on the original Unicode specification, which defined characters as fixed-width 16-bit entities. **The Unicode Standard has since been changed to allow for characters whose representation requires more than 16 bits**. The range of legal code points is now U+0000 to U+10FFFF, known as Unicode scalar value. (Refer to the definition of the U+n notation in the Unicode Standard.)
