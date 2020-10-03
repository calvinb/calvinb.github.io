---
layout: post
title: Haskell's State Monad
categories: haskell functional monads
---
<div class="kg-card-markdown"><p>Lately I've been looking into my own confusion around monads and side effects. In my last post I looked at the IO type constructor. Before I leave this topic behind, I think the State monad deserves a look.</p>
<p>While IO provides an abstraction over impure interaction with the outside world, State provides an abstraction over some state held in memory.</p>
<p>In languages that sport single assignment of variables, as most of our favorite functional languages do, we run into a slight headache when we need to do stateful computations. For example, think about how we interact with dictionaries. We might do something like start with an empty one, add a key-value pair or two, look up the value for a key, and then delete a key.</p>
<p>In an imperative language that allows mutation of variable values, this would be very straightforward. Here's what it might look like in Ruby:</p>
<pre><code class="language-prettyprint">states = Hash.new
states['TN'] = 'Tennessee'
states['CA'] = 'California'
tn_name = states['TN']
states.delete 'CA'
</code></pre>
<p>Most of the lines after the creation of the Hash object mutate its state. This would not be allowed in most functional settings. For example, in Erlang the update-oriented operations return a new dictionary since they cannot mutate the old one. The same program in Erlang might look like this:</p>
<pre><code class="language-prettyprint">States1 = dict:new().
States2 = dict:append(&quot;TN&quot;, &quot;Tennessee&quot;, States1).
States3 = dict:append(&quot;CA&quot;, &quot;California&quot;, States2).
{ok, [TNName]} = dict:find(&quot;TN&quot;, States3).
States4 = dict:erase(&quot;CA&quot;, States3).
</code></pre>
<p>We are able to do the same thing, but for the benefits of immutability we have a price to pay in intuitiveness. Haskell's State monad offers us a solution to this problem. It enables us abstract away the creation of a new variable for each state change. It doesn't allow mutation, but it allows us to use a syntax that looks like it does.</p>
<p>Before we get into the magic sugar version, let's write this code one more time in Haskell with explicit state management via new variables for changes.</p>
<pre><code class="language-pretty-print">import Data.Map
let states = empty
let states' = insert &quot;TN&quot; &quot;Tennessee&quot; states
let states'' = insert &quot;CA&quot; &quot;California&quot; states'
let tnName = states' ! &quot;TN&quot;
let states''' = delete &quot;CA&quot; states''
</code></pre>
<p>As you can see, this code appears to be concerned much more with managing versions of our state map than with the data it contains. Immutability is great, but in this case it would be nice to have it not quite so much in our faces in every single line. It would be great if we could keep our map in a state context that we could use implicitly without diverting all our attention from the primary intention of our code.</p>
<p>Fortunately, Haskell is particularly great at dealing with all kinds of contexts, such as non-determinism, possible failure, errors, impurity, etc. In this case our context is simply a state value held in memory.</p>
</div>
