---
layout: post
title: The char Type in Java is Broken
comments: True
excerpt_separator: <!--more-->
---

The `char` data type in Java is broken. When it first arrived, it was useful. But over the years as things changed, and the Unicode grew, it has become irrelevant. I avoid it. You probably should avoid it too.

Java supports Unicode and Strings are internally represented as UTF-16. Most characters in UTF-16 require 16 bits, but can take take up to 4 bytes if the character cannot fit into the 16-bit space. This is great and everything works as expected.

Now let's look at the `char` type. According to the [official documentation](https://docs.oracle.com/javase/tutorial/java/nutsandbolts/datatypes.html):

> char: The char data type is a single **16-bit Unicode character**. It has a minimum value of '\u0000' (or 0) and a maximum value of '\uffff' (or 65,535 inclusive).

*"16-bit Unicode character"?* Joel was right about it being the single most [common myth about Unicode](http://www.joelonsoftware.com/articles/Unicode.html):

>  Some people are under the misconception that Unicode is simply a 16-bit code where each character takes 16 bits and therefore there are 65,536 possible characters. **This is not, actually, correct**. It is the single most common myth about Unicode, so if you thought that, don't feel bad.

<!--more-->

Unicode isn't 16 bits. It never was. But in Sun's defense, when Java first arrived, Unicode characters fit perfectly within the 16-bit space with some room to grow. But over the years as languages, hieroglyphs, emojis and cool characters like these 🐵 ☠ ᧻ൠ ☕ were added, **Unicode outgrew the 16-bit space and now requires 21-bits** to represent all the characters.

So the `char` data type isn't a UTF-16 character, or arguably, even a Unicode character anymore. It's an *integer type* that stores values from 0 to 65535. The latest version of Unicode has 120,737 characters. The `char` type would fit only about half the latest Unicode characters. When you use methods like `someString.charAt(0)` or `someString.substring(0,1)`, you might only end up with half the character or half the code point and Java won't even complain. (Use [`codePointAt(index)`](https://docs.oracle.com/javase/7/docs/api/java/lang/String.html#codePointAt(int)) instead which returns an int that will fit all Unicode characters in existence.)

I have heard people say things like: *"if internationalization isn't a concern, you'd probably be fine using `char`"* or  *"don't worry about it unless your program is going to be released in China or Japan"*. This statement is wrong on two levels. First, at least I rarely come across applications where internationalization isn't a concern these days. Second, emoji Unicode characters that require more than 16-bits (👦👩) are used by audiences all over the world. Recently, a fellow developer told me that their "North American users" started complaining that the chat nicknames and messages "aren't displaying properly". After a lot of grief, they found the issue and had to undo all `char` manipulation in their software to handle emojis and other cool characters.

May be, a UTF-16 `char` type from Oracle would be the answer or at least a run-time exception when Java detects this anomaly. Until then, we should probably avoid the `char` type. Even the official [JavaDocs](https://docs.oracle.com/javase/7/docs/api/java/lang/Character.html) don't sound all that convincing:

> The char data type (and therefore the value that a Character object encapsulates) **are based on the original Unicode specification, which defined characters as fixed-width 16-bit entities. The Unicode Standard has since been changed** to allow for characters whose representation requires more than 16 bits. The range of legal code points is now U+0000 to U+10FFFF, known as Unicode scalar value. (Refer to the definition of the U+n notation in the Unicode Standard.)
