---
layout: post
title: Magic Not in the Monad
date: 2014-05-13 02:46:00.000000000 -05:00
categories: haskell monads
---
<div class="kg-card-markdown"><img src="http://upload.wikimedia.org/wikipedia/commons/c/c2/DeeHieroglyph.gif" alt="Monad" style="width:100px" />
<p>I am recently coming to grips with a foundational misconception I have been entertaining about monads, possibly since I first heard the term. I am not a category theorist, and I first encountered monads in the context of functional programming. For me the monad has always been shrouded in mystery and sporting a reputation of being very difficult to understand.</p>
<p>My misconception is that I have associated monads with side effects. Before I started learning Haskell, I heard someone say, &quot;If you need to produce a side effect in Haskell, you have to use a monad.&quot; This is not untrue, but the way it is stated is misleading. It would be more accurate to be more specific and say you have to use the IO type constructor. But IO is a monad, so where's the harm?</p>
<p>It didn't help me that IO is so often referred to as &quot;the IO monad.&quot; IO's imperative side effect business makes it a unique thing in the Haskell world. I attributed at least some of that uniqueness to its monad-ness, but the two have nothing to do with one another. Other monads do not have side effects, and IO can wield its side effects without being treated like a monad.</p>
<p>The epiphany of my error occurred during a recent <a href="http://nashfp.org">NashFP</a> discussion in which I found myself surprised to see that Maybe was a monad. The Maybe type class, being perhaps the simplest example of a context, is a great case to consider since there is minimal magic to get distracted by. How could Maybe be a monad when monads are magic and Maybe is not? I had to take a look and see what other non-magical monads were hanging around.</p>
<p>We can use the <code>:info</code> command to ask ghci (Glasgow Haskell Compiler Interactive) what Monad instances it has loaded right out of the box.</p>
<pre><code class="language-prettyprint">Prelude&gt; :info Monad
class Monad m where
  (&gt;&gt;=) :: m a -&gt; (a -&gt; m b) -&gt; m b
  (&gt;&gt;) :: m a -&gt; m b -&gt; m b
  return :: a -&gt; m a
  fail :: String -&gt; m a
  	-- Defined in `GHC.Base'
instance Monad Maybe -- Defined in `Data.Maybe'
instance Monad (Either e) -- Defined in `Data.Either'
instance Monad [] -- Defined in `GHC.Base'
instance Monad IO -- Defined in `GHC.Base'
instance Monad ((-&gt;) r) -- Defined in `GHC.Base'
</code></pre>
<p>Here we see first the four functions of the Monad type class, and then the instances, which are what we're after. There are five Monad instances in GHC.Base, and they are Maybe, Either, list, IO, and function. Yeah, the syntax is weird, but <code>((-&gt;) r)</code> is the <em>function</em> function. Functions are monads. Go chew on that one awhile. Every one of these Monad instances except IO is utterly commonplace and non-magical.</p>
<p>So there is monad without magic. How about magic without monad? What do we know about IO other than that it's a Monad instance? Let's ask ghci.</p>
<pre><code class="language-prettyprint">Prelude&gt; :info IO
newtype IO a
  = GHC.Types.IO (GHC.Prim.State# GHC.Prim.RealWorld
                  -&gt; (# GHC.Prim.State# GHC.Prim.RealWorld, a #))
  	-- Defined in `GHC.Types'
instance Monad IO -- Defined in `GHC.Base'
instance Functor IO -- Defined in `GHC.Base'
</code></pre>
<p>Of course! It's a Functor, as Monads almost always are. Let's talk to it like a plain old Functor. How do we talk Functor? Back to ghci.</p>
<pre><code class="language-prettyprint">Prelude&gt; :info Functor
class Functor f where
  fmap :: (a -&gt; b) -&gt; f a -&gt; f b
  (GHC.Base.&lt;$) :: a -&gt; f b -&gt; f a
  	-- Defined in `GHC.Base'
instance Functor Maybe -- Defined in `Data.Maybe'
instance Functor (Either a) -- Defined in `Data.Either'
instance Functor [] -- Defined in `GHC.Base'
instance Functor IO -- Defined in `GHC.Base'
instance Functor ((-&gt;) r) -- Defined in `GHC.Base'
instance Functor ((,) a) -- Defined in `GHC.Base'
</code></pre>
<p>Ah, yes, <code>fmap</code> is the only function in the Functor type class. If IO is just a Functor, we should be able to <code>fmap</code> over it. What is the simplest way to get an IO value? My favorite is the <code>getLine</code> function, which will give us an <code>IO String</code>.</p>
<pre><code class="language-prettyprint">Prelude&gt; :type getLine
getLine :: IO String
</code></pre>
<p>To <code>fmap</code> over an <code>IO String</code>, we need a function that takes a String and returns a simple value. Let's use <code>(++&quot;!&quot;)</code>, which will &quot;shout&quot; any string we apply it to. A simple use would look like this:</p>
<pre><code class="language-prettyprint">Prelude&gt; (++&quot;!&quot;) &quot;Hi&quot;
&quot;Hi!&quot;
</code></pre>
<p>Now let's try to <code>fmap</code> that function over an IO String, which we will get from <code>getLine</code>.</p>
<pre><code class="language-prettyprint">Prelude&gt; fmap (++&quot;!&quot;) getLine
Hello
&quot;Hello!&quot;
</code></pre>
<p>Look at that. We just used IO as a Functor (via <code>fmap</code>) without using any Monad functions or features. It still waited for input. It still got an unpredictable value. It still did its magic thing as a plain old Functor.</p>
<p>So there we have it. The magic is in IO alone, not in Monad. I'll try to write more soon about what the monad mojo <em>is</em>, now that we have spent some time on what it is <em>not</em>.</p>
</div>
