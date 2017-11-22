---
layout: post
title: Magic Not in the Monad
date: 2014-05-13 02:46:00.000000000 -05:00
categories: haskell monads
---
<img src="http://upload.wikimedia.org/wikipedia/commons/c/c2/DeeHieroglyph.gif" alt="Monad" style="width:100px" />

I am recently coming to grips with a foundational misconception I have been entertaining about monads, possibly since I first heard the term. I am not a category theorist, and I first encountered monads in the context of functional programming. For me the monad has always been shrouded in mystery and sporting a reputation of being very difficult to understand.

My misconception is that I have associated monads with side effects. Before I started learning Haskell, I heard someone say, "If you need to produce a side effect in Haskell, you have to use a monad." This is not untrue, but the way it is stated is misleading. It would be more accurate to be more specific and say you have to use the IO type constructor. But IO is a monad, so where's the harm?

It didn't help me that IO is so often referred to as "the IO monad." IO's imperative side effect business makes it a unique thing in the Haskell world. I attributed at least some of that uniqueness to its monad-ness, but the two have nothing to do with one another. Other monads do not have side effects, and IO can wield its side effects without being treated like a monad.

The epiphany of my error occurred during a recent [NashFP](http://nashfp.org) discussion in which I found myself surprised to see that Maybe was a monad. The Maybe type class, being perhaps the simplest example of a context, is a great case to consider since there is minimal magic to get distracted by. How could Maybe be a monad when monads are magic and Maybe is not? I had to take a look and see what other non-magical monads were hanging around.

We can use the `:info` command to ask ghci (Glasgow Haskell Compiler Interactive) what Monad instances it has loaded right out of the box.

{% highlight haskell %}
Prelude> :info Monad
class Monad m where
  (>>=) :: m a -> (a -> m b) -> m b
  (>>) :: m a -> m b -> m b
  return :: a -> m a
  fail :: String -> m a
    -- Defined in `GHC.Base'
instance Monad Maybe -- Defined in `Data.Maybe'
instance Monad (Either e) -- Defined in `Data.Either'
instance Monad [] -- Defined in `GHC.Base'
instance Monad IO -- Defined in `GHC.Base'
instance Monad ((->) r) -- Defined in `GHC.Base'
{% endhighlight %}

Here we see first the four functions of the Monad type class, and then the instances, which are what we're after. There are five Monad instances in GHC.Base, and they are Maybe, Either, list, IO, and function. Yeah, the syntax is weird, but `((->) r)` is the _function_ function. Functions are monads. Go chew on that one awhile. Every one of these Monad instances except IO is utterly commonplace and non-magical.

So there is monad without magic. How about magic without monad? What do we know about IO other than that it's a Monad instance? Let's ask ghci.

{% highlight haskell %}
Prelude> :info IO
newtype IO a
  = GHC.Types.IO (GHC.Prim.State# GHC.Prim.RealWorld
                  -> (# GHC.Prim.State# GHC.Prim.RealWorld, a #))
    -- Defined in `GHC.Types'
instance Monad IO -- Defined in `GHC.Base'
instance Functor IO -- Defined in `GHC.Base'
{% endhighlight %}

Of course! It's a Functor, as Monads almost always are. Let's talk to it like a plain old Functor. How do we talk Functor? Back to ghci.

{% highlight haskell %}
Prelude> :info Functor
class Functor f where
  fmap :: (a -> b) -> f a -> f b
  (GHC.Base.<$) :: a -> f b -> f a
    -- Defined in `GHC.Base'
instance Functor Maybe -- Defined in `Data.Maybe'
instance Functor (Either a) -- Defined in `Data.Either'
instance Functor [] -- Defined in `GHC.Base'
instance Functor IO -- Defined in `GHC.Base'
instance Functor ((->) r) -- Defined in `GHC.Base'
instance Functor ((,) a) -- Defined in `GHC.Base'
{% endhighlight %}

Ah, yes, `fmap` is the only function in the Functor type class. If IO is just a Functor, we should be able to `fmap` over it. What is the simplest way to get an IO value? My favorite is the `getLine` function, which will give us an `IO String`.

{% highlight haskell %}
Prelude> :type getLine
getLine :: IO String
{% endhighlight %}

To `fmap` over an `IO String`, we need a function that takes a String and returns a simple value. Let's use `(++"!")`, which will "shout" any string we apply it to. A simple use would look like this:

{% highlight haskell %}
Prelude> (++"!") "Hi"
"Hi!"
{% endhighlight %}

Now let's try to `fmap` that function over an IO String, which we will get from `getLine`.

{% highlight haskell %}
Prelude> fmap (++"!") getLine
Hello
"Hello!"
{% endhighlight %}

Look at that. We just used IO as a Functor (via `fmap`) without using any Monad functions or features. It still waited for input. It still got an unpredictable value. It still did its magic thing as a plain old Functor.

So there we have it. The magic is in IO alone, not in Monad. I'll try to write more soon about what the monad mojo _is_, now that we have spent some time on what it is _not_.
