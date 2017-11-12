---
layout: post
title: Partial Application vs. Currying
date: 2014-06-28 03:06:00.000000000 -05:00
---
<div class="kg-card-markdown"><p><img src="http://www.foodpeoplewant.com/wp-content/uploads/2009/11/Panang-Beef-Curry-1024x768.jpg" alt="Curry"></p>
<p>Is the entire world of aspiring functional programmers generally confused about partial function application and currying, or is it just me? I like to feel well-informed when I think to myself that these two are not the same thing, but I still couldn't tell you what the difference is. To me they are like Bill and Ted on their Excellent Adventure; I know them when I see them, but I don't know which is which. I have just decided it is time to dive in and get this straight. So here we go.</p>
<h3 id="divideandconquer">Divide and Conquer</h3>
<p>Partial function application and currying both have to do with decomposing multi-parameter functions into smaller functions with fewer parameters. For example, in Haskell we might have an <em>add</em> function that takes two integers and returns their sum:</p>
<pre><code class="language-prettyprint">add :: Integer -&gt; Integer -&gt; Integer
add x y = x + y
</code></pre>
<p>If I had this function available and wanted another function that took one number and added 3 to it, I could do this:</p>
<pre><code class="language-prettyprint">add3 :: Integer -&gt; Integer
add3 = add 3
</code></pre>
<p>Now this function does just what we want.</p>
<pre><code class="language-prettyprint">add3 2    --&gt; 5
</code></pre>
<p>When I see this kind of thing, I can say with confidence, &quot;Ah, that's that currying/partial application deal.&quot; But which is it? Either one? Both?</p>
<p>After spending a little time digging in, I think my main problem here is that I'm only concerned about these concepts when working in languages whose functions support it implicitly, in my case Haskell and F#. I get these two bits of functional magic free in a gift bag, but since they're always wrapped up together, they feel like one thing instead of two.</p>
<h3 id="breakitdown">Break It Down</h3>
<p>Of the two concepts, partial application is the easier to understand, thanks to its meaningful name. It is just what it sounds like. You have a function that takes more than one parameter, you pass it fewer parameters than it wants, and it returns a new function that will take the remaining parameters and return you your result, as we just demonstrated with our <em>add3</em> function. It is partially applied, applied to some of its parameters with the option to apply the resulting function to the rest later.</p>
<p>So what is currying? Currying is the transformation of one multi-parameter function into a chain of single-parameter functions that ultimately yield the same result. The problem with trying to discuss currying in Haskell and F# is that they don't have multi-parameter functions in first place. A function such as <em>add</em>, which logically has multiple parameters, is defined implicitly as a chain of single-parameter functions, or in the <em>curried</em> style. So when does the currying take place? I would have to say at compile time. No wonder I was missing it!</p>
<p>In Haskell and F#, currying is more their style of implementing multi-parameter functions than it is a technique that we use when writing in these languages. The functions are curried, period, whether or not we choose to partially apply them, which is the only time we would think to notice.</p>
<h3 id="buildit">Build It</h3>
<p>To get a feel for what is involved here, we need to get away from this implicit currying and go somewhere we have support for true multi-parameter functions, and we can build this stuff ourselves. JavaScript, anyone? JavaScript, like most imperative languages, and unlike Haskell and F#, supports true (non-curried) multi-parameter functions. (Its <em>bind</em> function provides partial application support as well, but we still want to build our own so we can see what's happening.) Let's start with our <em>add</em> function again.</p>
<pre><code class="language-prettyprint">var add = function (x, y) { return x + y; };
</code></pre>
<p>Now we can build a function to partially apply another two-parameter function by taking it and its first parameter, and returning a new function that takes the second parameter and returns the result.</p>
<pre><code class="language-prettyprint">var papply = function (fn2, x) {
	return function (y) { return fn2 (x, y); };
};
</code></pre>
<p>Now we can use this function to build our <em>add3</em> function by partially applying <em>add</em>.</p>
<pre><code class="language-prettyprint">var add3 = papply(add, 3);
var five = add3(2);  //--&gt; 5
</code></pre>
<p>Notice how that was more work than partially applying in Haskell? That's because a curried function gives you partial application pretty much free; the single-parameter component functions are already in there, just waiting to be let out. Let's build a <em>curry</em> function in JavaScript to demonstrate this. Remember currying is the transformation of a multi-parameter function into a chain of single-parameter functions, so our <em>curry</em> function will take a function and return a function. Let's assume our input function takes two parameters, like our <em>add</em>.</p>
<pre><code class="language-prettyprint">var curry = function (fn2) {
	return function (x) {
    	return function (y) {
        	return fn2(x, y);
        };
    };
};
</code></pre>
<p>We can use this function to create a curried version of <em>add</em>.</p>
<pre><code class="language-prettyprint">var cAdd = curry(add);
</code></pre>
<p>This function will return the same results as <em>add</em> but has to be called differently, since it is a chain of single-parameter functions.</p>
<pre><code class="language-prettyprint">var five = cAdd(3)(2);  //--&gt; 5
</code></pre>
<p>Now back to the idea of how the curried style gives us partial application free of charge. Look at how we can partially apply our <em>cAdd</em>:</p>
<pre><code class="language-prettyprint">var add4 = cAdd(4);
var seven = add4(3);  //--&gt; 7
</code></pre>
<p>Do you see what we've got here? Currying is a way of defining a function; partial application is a way of applying a function. Currying is about construction; partial application is about consumption, with a by-product. Some functions are curried, but no functions are partial.</p>
<h3 id="undo">Undo</h3>
<p>In the words of Blood, Sweat, and Tears, what goes up must come down, and curried functions can be uncurried (in languages that support multi-parameter functions). Uncurrying would be the transformation of a chain of single-parameter functions into one multi-parameter function. Here is a function that will do that for us:</p>
<pre><code class="language-prettyprint">var uncurry = function (cfn2) {
	return function (x, y) {
    	return cfn2(x)(y);
    };
};
</code></pre>
<p>Now we could use this function to create a new <em>add</em> function from our curried <em>cAdd</em>.</p>
<pre><code class="language-prettyprint">var newAdd = uncurry (cAdd);
var eight = newAdd(5, 3);  //--&gt; 8
</code></pre>
<p>Excellent! We have separated partial application from currying. I think I might be able to remember the difference now. I hope you will, too.</p>
</div>
