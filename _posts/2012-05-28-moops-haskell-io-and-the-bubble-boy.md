---
layout: post
title: Moops! Haskell, IO, and the Bubble Boy
date: 2012-05-28 08:53:00.000000000 -05:00
categories: haskell functional
video: //www.youtube.com/embed/LQzW73WRrYM
---
I recently set out to get Haskell connected to a PostgreSQL database. The task ended up having almost nothing to do with PostgreSQL and everything to do with Haskell's militant stance on functional purity. I chose to use HDBC since it appeared to be the most vanilla approach. Before I even got to the point of trying to open a database connection, I was confronted with a philosophical conundrum not too different from the sound of one hand clapping.

I'll provide some code here in case you want to follow along in ghci (the Glasgow Haskell Compiler Interactive), but I'm really more about the philosophy here. First I imported the HDBC library and the PostgreSQL driver.
<pre class="prettyprint">ghci&gt; import Database.HDBC
ghci&gt; import Database.HDBC.PostgreSQL</pre>
Next, I see that the library has a function called connectPostgreSQL whose job is to open a connection for me. I have always admired Haskell's commitment to functional purity, even though I have wondered sometimes whether that rendered it useless in real life. Haskell functions operate under the same constraints as mathematical functions. According to Wikipedia, in mathematics, a <a href="http://en.wikipedia.org/wiki/Function_(mathematics)" target="_blank">function</a> is, "a relation between a set of inputs and a set of potential outputs with the property that each input is related to exactly one output." A function, given a particular input, will always return the same result. Pure functional programming adds the constraint that a function will have no side effects.

I have connected several programming languages to databases, and they have mostly worked in the same general way. You call a function or method with some basic connection information, it opens a connection, and you get back an object reference or a handle that can subsequently be used to interact with the connection that has been opened. How will this work in a pure functional language? Opening a database connection is a side effect in the first place, so we're already breaking the rules, and we haven't even done anything yet.

Let's ignore that for the moment and think about what the function's result should be. What type of value is this function going to give me? Once I have my connection details defined and pass them to this function, it should return me a handle to an open database connection, right? Well, it will if the connection information is correct, and the server is running, and I have a network connection to it, and my password hasn't expired, etc. This exact function call might give me a connection one time, but then I can turn off my wireless and call the function again, and I will get a very different result. Mathematics would tell me this is not a function. Haskell would agree.

This whole operation of opening a database connection is tainted. It is not a mathematical operation. It is not a functional operation. It is imperative by nature. It is entirely side-effect driven. It is not about getting an answer; it is about changing something in the world. Haskell does not take this lightly. It does not prevent you from doing it, but it does not allow such tainted operations to pollute its pristine functions. Such imperative, side-effect-riddled operations are relegated to a language feature known as the IO action. (The IO action is technically a monad, but that's a topic for another day.) The connectPostgreSQL function by definition cannot return me a handle to a connection that it may or may not be able to establish, but it can reliably return me an IO action that by definition behaves imperatively and unpredictably. This IO action is our window into the cruel outside world. It is our bubble.

Haskell is the bubble boy. In his world there are no germs, no file systems, no databases, no spotty networks, only pure math. The IO action is his window to the outside world, his bubble. He can see through it, talk through it, and use those hose sleeves and rubber gloves to play Trivial Pursuit through it, but he can never actually touch anything. If did, he would be contaminated, infected, tainted, impure. The bubble keeps him safely insulated from all the brutal unpredictability of the outside world.

So the connectPostgreSQL function can't return me a database connection, but it can predictably and consistently return me an IO action, which is by definition tainted, impure, unpredictable, and unreliable, and this IO action just might, in the perfect alignment of the stars and all favorable circumstances, contain a database connection for me. We can ask ghci for the type of the function like this:
<pre class="prettyprint">ghci&gt; :type connectPostgreSQL
connectPostgreSQL :: String -&gt; IO Connection</pre>
Here we see that it takes a String (providing connection details) and returns an IO action that might give us a Connection. Once again, this function will always succeed. The perils of the impurity of the outside world pose us no threat until we ask the IO action for the connection. We can do this with the &lt;- operator.
<pre class="prettyprint">ghci&gt; let io = connectPostgreSQL "dbname=mydb password=secret"
ghci&gt; :type io
io :: IO Connection
ghci&gt; -- Here's the scary part:
ghci&gt; conn &lt;- io
ghci&gt; -- Hey, it worked!
ghci&gt; :type conn
conn :: Connection</pre>
If we have made it this far, we now have a connection handle that we can use to execute queries. There's a function here called quickQuery for doing just that. Of course, the connection could be severed at any time by forces entirely beyond our control. Even if the connection could be reliable, the same select query may very well yield a different result every time I execute it. That's kind of the point of having a database, right? I think you know what I'm gettting at here. If the operation's result is unpredictable, it can't be a function.

Once again, IO action to the rescue. quickQuery can still be a function if it predictably returns an IO action instead of a surprise from some flaky database.
<pre class="prettyprint">ghci&gt; :type quickQuery
quickQuery
  :: IConnection conn =&gt;
     conn -&gt; String -&gt; [SqlValue] -&gt; IO [[SqlValue]]</pre>
This tells us that quickQuery takes a conn (which must be some type of IConnection), a string (for the query), and a list of SqlValues (for proper query parameters to avoid SQL injection attacks), and it returns us an IO action. This IO action, however, may contain in its tainted wildness not a database connection but a list of lists of SqlValues. Of course the outer list can be seen as list of rows, and the inner lists as lists of column values.
<pre class="prettyprint">ghci&gt; let resultIO = quickQuery conn "select count(*) from some_table" []
ghci&gt; :type resultIO
resultIO :: IO [[SqlValue]]
ghci&gt; --That was safe: now the scary part:
ghci&gt; result &lt;- resultIO
ghci&gt; --Whew, got lucky again.
ghci&gt; :type result
result :: [[SqlValue]]
ghci&gt; result
[[SqlInteger 4]]</pre>
At this moment we can see that some_table has 4 rows in it. When I make that exact function call again, it might have 5, but it doesn't compromise the purity of the function because that number is not the function's result. That number is the result of executing an IO action, which does not pretend to have the determinism of a function. The pure function has the IO action do its dirty work.

Life is unpredictable, but mathematics is not. The IO action gives Haskell the means to exploit the predictability of its mathematical bias but still interact with the unpredictable outside world, thereby making it, well, useful.
