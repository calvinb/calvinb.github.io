---
layout: post
title: 'Anatomy of a One-Liner: FizzBuzz in Haskell'
date: 2012-02-25 10:21:00.000000000 -06:00
---
<div class="kg-card-markdown"><p>In the <a href="https://groups.google.com/group/nashfp/" target="_blank">NashFP</a> group, a gang of functionally inclined Nashville area geeks recently organized by <a href="http://codeswamp.com/" target="_blank">Bryan Hunter</a>, we decided to share <a href="https://github.com/NashFP/fizzbuzz" target="_blank">implementations</a> of the classic FizzBuzz problem in various functional languages. I took my first shot in Scheme and then decided it was time to dust off my Haskell. On my first attempt I waded through the syntax enough to get something working, but it included several named functions and was clearly more code than was necessary to solve the problem. As I began to look for ways to shorten it, I remembered that a year or two ago Bryan had written a FizzBuzz implementation in Erlang that was short enough to tweet. Obviously this became my new goal.</p>
<p>I managed to whittle it down below 140 characters, but its readability suffered quite a bit. I thought it might be fun to dissect here bit by bit, and it might even come out shorter and more readable. I keep learning new tricks in Haskell and in the general functional approach that offer new opportunities to make my code shorter and more streamlined. You might learn some of these here, or if you have any pointers to offer me in comments, I would love to learn some from you as well.</p>
<p>In case you're not familiar with FizzBuzz, it is a programming exercise in which goal is to list integers from 1 to 100, replacing those divisible by 3 with the term &quot;Fizz,&quot; those divisible by 5 with the term &quot;Buzz,&quot; and those divisible by both 3 and 5 with the term &quot;FizzBuzz.&quot;</p>
<p>For this exercise I will be using my favorite IDE, the REPL, in this case GHCI, the Glasgow Haskell Compiler Interactive.</p>
<p>Let's start with the input range. We ultimately want to go from 1 to 100, but let's start with 1 to 15 just for brevity of output while we evolve this thing.</p>
<pre class="prettyprint">Prelude&gt; [1..15]
[1,2,3,4,5,6,7,8,9,10,11,12,13,14,15]</pre>
<p>To apply a function to each member of this list, we could use the map function or a list comprehension, which should be shorter, so let's try that. We'll start with a simple identity comprehension, which will do nothing for us. This step is just about making ourselves a place to do some work.</p>
<pre class="prettyprint">Prelude&gt; [x | x &lt;- [1..15]]
[1,2,3,4,5,6,7,8,9,10,11,12,13,14,15]</pre>
<p>Let's try the Fizz substitution for multiples of 3. Note that the else case needs the show function to convert the integer to a string since Haskell lists must be homogenous.</p>
<pre class="prettyprint">Prelude&gt; [if mod x 3 == 0 then "Fizz" else show x | x &lt;- [1..15]]
["1","2","Fizz","4","5","Fizz","7","8","Fizz","10","11","Fizz","13","14","Fizz"]</pre>
<p>Now for the Buzz case for multiples of 5:</p>
<pre class="prettyprint">Prelude&gt; [if mod x 3 == 0 then "Fizz" else if mod x 5 == 0 then "Buzz" else show x | x &lt;- [1..15]]
["1","2","Fizz","4","Buzz","Fizz","7","8","Fizz","Buzz","11","Fizz","13","14","Fizz"]</pre>
<p>This works up through 14, but I hate to add another case, and it's already getting too long due to repetitive code. Under normal circumstances I might look to extract a method here, but we're working on a one-liner, so let's try another way. The factor mappings must be stated, and the most concise way I know is as a list of tuples.</p>
<pre class="prettyprint">Prelude&gt; [(3,"Fizz"),(5,"Buzz")]
[(3,"Fizz"),(5,"Buzz")]</pre>
<p>Now we need a way to iterate through this list, run the divisibility test, and make the appropriate substitutions. First off we need to give the mapping list a chance to dance with each element in the list of integers. We can do this by tucking it into a tuple with the integer inside the comprehension.</p>
<pre class="prettyprint">Prelude&gt; [(x,[(3,"Fizz"),(5,"Buzz")]) | x &lt;- [1..15]]
[(1,[(3,"Fizz"),(5,"Buzz")]),(2,[(3,"Fizz"),(5,"Buzz")]),(3,[(3,"Fizz"),(5,"Buzz")]),(4,[(3,"Fizz"),(5,"Buzz")]),(5,[(3,"Fizz"),(5,"Buzz")]),(6,[(3,"Fizz"),(5,"Buzz")]),(7,[(3,"Fizz"),(5,"Buzz")]),(8,[(3,"Fizz"),(5,"Buzz")]),(9,[(3,"Fizz"),(5,"Buzz")]),(10,[(3,"Fizz"),(5,"Buzz")]),(11,[(3,"Fizz"),(5,"Buzz")]),(12,[(3,"Fizz"),(5,"Buzz")]),(13,[(3,"Fizz"),(5,"Buzz")]),(14,[(3,"Fizz"),(5,"Buzz")]),(15,[(3,"Fizz"),(5,"Buzz")])]</pre>
<p>The resulting list can now be used as the input for another list comprehension, where we can take another shot at applying our divisibility tests. Once again we will start with an identity comprehension just to get our terms in place before we put them to work. Since each element of the list is a 2-tuple, we can go ahead and give each member a variable, f for factor and n for name.</p>
<pre class="prettyprint">Prelude&gt; [(x,[(f,n) | (f,n) &lt;- [(3,"Fizz"),(5,"Buzz")]]) | x &lt;- [1..15]]
[(1,[(3,"Fizz"),(5,"Buzz")]),(2,[(3,"Fizz"),(5,"Buzz")]),(3,[(3,"Fizz"),(5,"Buzz")]),(4,[(3,"Fizz"),(5,"Buzz")]),(5,[(3,"Fizz"),(5,"Buzz")]),(6,[(3,"Fizz"),(5,"Buzz")]),(7,[(3,"Fizz"),(5,"Buzz")]),(8,[(3,"Fizz"),(5,"Buzz")]),(9,[(3,"Fizz"),(5,"Buzz")]),(10,[(3,"Fizz"),(5,"Buzz")]),(11,[(3,"Fizz"),(5,"Buzz")]),(12,[(3,"Fizz"),(5,"Buzz")]),(13,[(3,"Fizz"),(5,"Buzz")]),(14,[(3,"Fizz"),(5,"Buzz")]),(15,[(3,"Fizz"),(5,"Buzz")])]</pre>
<p>Now we can add a filter to the new comprehension to get only the pairs whose factor evenly divides the integer.</p>
<pre class="prettyprint">Prelude&gt; [(x,[(f,n) | (f,n) &lt;- [(3,"Fizz"),(5,"Buzz")],mod x f == 0]) | x &lt;- [1..15]]
[(1,[]),(2,[]),(3,[(3,"Fizz")]),(4,[]),(5,[(5,"Buzz")]),(6,[(3,"Fizz")]),(7,[]),(8,[]),(9,[(3,"Fizz")]),(10,[(5,"Buzz")]),(11,[]),(12,[(3,"Fizz")]),(13,[]),(14,[]),(15,[(3,"Fizz"),(5,"Buzz")])]</pre>
<p>That's closer, but that comprehension is still returning the factor-name pair, and we're only interested in the name, so let's make that adjustment.</p>
<pre class="prettyprint">Prelude&gt; [(x,[n | (f,n) &lt;- [(3,"Fizz"),(5,"Buzz")],mod x f == 0]) | x &lt;- [1..15]]
[(1,[]),(2,[]),(3,["Fizz"]),(4,[]),(5,["Buzz"]),(6,["Fizz"]),(7,[]),(8,[]),(9,["Fizz"]),(10,["Buzz"]),(11,[]),(12,["Fizz"]),(13,[]),(14,[]),(15,["Fizz","Buzz"])]</pre>
<p>This is starting to bear some resemblance to the result we're after, but notice that our Fizzes and Buzzes are still coming out in lists. We might grab the head of the list, but the FizzBuzzes have two elements, and we don't want to drop one. We need to concatenate them, which we can do with the concat function.</p>
<pre class="prettyprint">Prelude&gt; [(x,concat [n | (f,n) &lt;- [(3,"Fizz"),(5,"Buzz")],mod x f == 0]) | x &lt;- [1..15]]
[(1,""),(2,""),(3,"Fizz"),(4,""),(5,"Buzz"),(6,"Fizz"),(7,""),(8,""),(9,"Fizz"),(10,"Buzz"),(11,""),(12,"Fizz"),(13,""),(14,""),(15,"FizzBuzz")]</pre>
<p>Now we have a list of tuples, each containing one value that we want and another that we don't. First we'll add a show to the x to make them the same type so we can compare them.</p>
<pre class="prettyprint">Prelude&gt; [(show x,concat [n | (f,n) &lt;- [(3,"Fizz"),(5,"Buzz")],mod x f == 0]) | x &lt;- [1..15]]
[("1",""),("2",""),("3","Fizz"),("4",""),("5","Buzz"),("6","Fizz"),("7",""),("8",""),("9","Fizz"),("10","Buzz"),("11",""),("12","Fizz"),("13",""),("14",""),("15","FizzBuzz")]</pre>
<p>Now that the elements in the tuples are the same type, we can use the max function to make the choice between them.</p>
<pre class="prettyprint">Prelude&gt; [max (show x) (concat [n | (f,n) &lt;- [(3,"Fizz"),(5,"Buzz")],mod x f == 0]) | x &lt;- [1..15]]
["1","2","Fizz","4","Buzz","Fizz","7","8","Fizz","Buzz","11","Fizz","13","14","FizzBuzz"]</pre>
<p>Now all that's left to do is to remove some readability spaces to squeeze it down and blow up our input range to give us 100 inputs instead of 15.</p>
<pre class="prettyprint">Prelude&gt; [max(show x)(concat[n|(f,n)&lt;-[(3,"Fizz"),(5,"Buzz")],mod x f==0])|x&lt;-[1..100]]
["1","2","Fizz","4","Buzz","Fizz","7","8","Fizz","Buzz","11","Fizz","13","14","FizzBuzz","16","17","Fizz","19","Buzz","Fizz","22","23","Fizz","Buzz","26","Fizz","28","29","FizzBuzz","31","32","Fizz","34","Buzz","Fizz","37","38","Fizz","Buzz","41","Fizz","43","44","FizzBuzz","46","47","Fizz","49","Buzz","Fizz","52","53","Fizz","Buzz","56","Fizz","58","59","FizzBuzz","61","62","Fizz","64","Buzz","Fizz","67","68","Fizz","Buzz","71","Fizz","73","74","FizzBuzz","76","77","Fizz","79","Buzz","Fizz","82","83","Fizz","Buzz","86","Fizz","88","89","FizzBuzz","91","92","Fizz","94","Buzz","Fizz","97","98","Fizz","Buzz"]</pre>
<p>78 characters. Not too bad. Please let me know if you see where I could shave off some more.</p>
</div>
