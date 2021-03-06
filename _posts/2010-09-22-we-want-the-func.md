---
title: We Want the Func! - Moving Toward Functional Programming
date: 2010-09-22 01:31:00.000000000 -05:00
categories: functional
---
In the last year I have heard/read where <a href="http://www.objectmentor.com/omTeam/martin_r.html">Uncle Bob</a> has been talking about discovering the twenty-six-year-old treasure of a book <a href="http://en.wikipedia.org/wiki/Structure_and_Interpretation_of_Computer_Programs">The Structure and Interpretation of Computer Programs</a>. It happened enough times that I decided to check it out. The book is available free of charge <a href="http://www.scribd.com/doc/15556326/Structure-and-Interpretation-of-Computer-Programs-SICP">here</a>. The language used in the book is <a href="http://en.wikipedia.org/wiki/Scheme_%28programming_language%29">Scheme</a>, an implementation of <a href="http://en.wikipedia.org/wiki/Lisp_programming_language">Lisp</a>. I hadn't written or read any Lisp since college, but even then I really liked it.

At the time I had no idea of the concepts of functional vs. imperative programming. We didn't talk about immutability; I just thought it was interesting that you had to use recursion where you would normally have used a loop.

About fifteen years later I found myself learning XSLT. While here we do have the &lt;xsl:for-each&gt; element, I once again discovered that if I wanted to count from one to ten, I had to use recursion. That didn't throw me so badly, but what did throw me was the &lt;xsl:variable&gt; element. Maybe it was just the name and my own notions of how a variable is used. I thought I should be able to do this:

<pre>&lt;xsl:variable name="x" select="1" /&gt;
&lt;xsl:variable name="x" select="$x + 1" /&gt;
</pre>

But nope. Once x is defined, that's it. No one was there at that moment to offer me the term <i>immutability</i>, but there it was. Little did I know that a few years later, linear processing speed would be maxed out, having bumped its head on the laws of physics, and this concept would give traction to something of a programming revolution in pursuit of scaling via concurrency.

After a decade or so of studying <a href="http://en.wikipedia.org/wiki/Design_Patterns_%28book%29">Gang of Four</a> and <a href="http://martinfowler.com/books.html#eaa">Fowler</a>, honing our OO skills, now .NET geeks are delving into F#, JVM nerds are digging into Clojure and Scala, and Erlang and Haskell are finding their way out of the classroom and into the business world. Even though web developers have been writing client-side code in JavaScript for the last fifteen years or so, most of us never noticed what great functional capabilities it had until recently when jQuery showed us what kind of magic a more functional approach had to offer. And why should all that magic be confined to the client side? Along comes <a href="http://nodejs.org/">node.js</a>, bringing all that non-blocking functional goodness to the server, or wherever you might need it.

<a href="http://en.wikipedia.org/wiki/Anders_Hejlsberg">Anders Hejlsberg</a>, king of Turbo Pascal, Delphi, and C#, has been talking lately about the future of programming languages and how important functional capabilities will be to keep them moving forward. <a href="http://channel9.msdn.com/blogs/adebruyn/techdays-2010-developer-keynote-by-anders-hejlsberg">This video</a> is a little over an hour long, and it's all good, but if you don't have that much time, at least watch his overview of what functional programming is, which starts about 21 minutes in. It is excellent. He says so much with the simple phrase, "more like math." He also says some interesting things about "islands of functional purity" in the context of more conventional data-oriented (mutable-state) applications.

So what is my point here? Simply that there a lot of us who might just be starting to feel like we've made the transition from procedural to object-oriented programming, and we need to be aware of this functional movement and do what we can to embrace it and adapt to it. It's a very different way to think about solving problems, and it's a lot of fun. Onward and upward! There's more to life than inheritance.
