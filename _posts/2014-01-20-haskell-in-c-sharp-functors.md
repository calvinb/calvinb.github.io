---
title: 'Rebuilding Haskell in C#: Functors'
date: 2014-01-20 15:23:00.000000000 -06:00
categories: haskell functional c#
---
Look out; I've been digging into Haskell again. I enjoy learning about Haskell, but more than that, I enjoy learning to think in a functional way. Haskell is a particularly good language to facilitate that, as it is arguably the most functional of functional languages. It boasts "pure" functionality, and while some might believe that means it is of no practical use, I think it makes it pretty cool.

While I enjoy my Haskell studies, I still struggle with a few core concepts. For example, I keep having to go back and reread explanations of Functors. I understand them at the time, but it doesn't stick, and I keep having to come back and read it again. Maybe my appreciation for some of the staples of Haskell would be a little less volatile if I had the experience of implementing them myself in an imperative language like C#.

To implement Functors, there are a few building blocks we'll need first, and it will be helpful to establish a simple concept map between pieces of Haskell and C#. Let's start with one of the foundational constructs of the functional world, the <em>map</em> function. The type of Haskell's <em>map</em> function is defined like this:
{% highlight haskell %}
map :: (a -> b) -> [a] -> [b]
{% endhighlight %}
This means that the <em>map</em> function takes a function that takes a value of type <em>a</em> and returns a value of type <em>b</em>, and a list of values of type <em>a</em>, and returns a list of values of type <em>b</em>. In fewer words, it takes a function from <em>a</em> to <em>b</em>, and a list of <em>a</em>, and returns a list of <em>b</em>. It takes a function and a list of inputs to it, and returns a list of outputs. You've probably seen this kind of thing in other languages. I think I first encountered it in Ruby. Shortly thereafter I was thrilled to see that LINQ had brought this goodness to .NET in the form of the <em>Select</em> extension method on <em>IEnumerable</em>. Of course the LINQ implementation uses the very OO approach of adding an extension method onto the <em>IEnumerable</em> type. Its signature looks like this:
{% highlight csharp %}
public static IEnumerable<TResult> Select<TSource, TResult>(
    this IEnumerable<TSource> source,
    Func<TSource, TResult> selector
)
{% endhighlight %}
Stare at the two for a minute if you need to; the Haskell type and the C# method signature say exactly the same thing, only stated in reverse order from one another. (And C# takes a heck of a lot more notes to name that tune.)

So the two languages have some common ground to start with. Now we need to get a little bit of Haskell vocabulary under our belts. I will try to describe Haskell constructs in terms of C# constructs as much as possible.
<h3>Type Classes</h3>

Try to ignore the word <em>class</em>; it's not like an OO class. A type class in Haskell is closer to an interface in OO. Types can be instances of various type classes much like classes in C# can implement various interfaces. A type class comprises a list of functions, and any type that wants to be an instance of that type class must implement its functions. It might not be too much of a stretch to say it <em>is</em> interface implementation. Each instance type has its own unique implementation of each function, just as each implementing class has its own unique implementation of an interface's members, affording the system the same benefits of polymorphism that we have always enjoyed in OO.
<h3>Type Constructors</h3>

You can think of a type constructor as something like a generic type in .NET, such as <em>List&lt;T&gt;</em> or <em>Nullable&lt;T&gt;</em>. It can't stand alone as a type; it needs another type to make it complete. You can't instantiate a <em>List&lt;T&gt;</em> unless the identifier <em>T</em> is bound to an actual type such as <em>int</em> or <em>string</em>. Type constructors work the same way.
<h3>Functors</h3>

Haskell's <em>Functor</em> is a particular type class, and it's pretty simple as type classes go, in that it has only one function, <em>fmap</em>, but it wields some serious power. <em>fmap</em>'s type is defined like this:
{% highlight csharp %}
fmap :: Functor f => (a -> b) -> f a -> f b
{% endhighlight %}
This means that, given that a type constructor <em>f</em> is a functor, <em>fmap</em> takes a function that takes an <em>a</em> and returns a <em>b</em>, and an <em>f</em> of <em>a</em>, and returns an <em>f</em> of <em>b</em>. We could express this signature in C# like this:
{% highlight csharp %}
IFunctor<TResult> FMap<TSource, TResult>(
    Func<TSource, TResult> func,
    IFunctor<TSource> input
)
{% endhighlight %}
It is commonly stated that functors are structures than can be "mapped over." They establish a context that contains a particular type of value, and then they can take a function, apply it to the contained value, and wrap up the result in the same type of context again, returning the new context as the result. Think of the <em>map</em> function we just looked at. It maps functions over lists. It pulls a value out of a list, applies a function to it, and puts the result into a list. Hey, if lists can be mapped over, does this mean that lists are functors? Why, yes, it does. Take another look at the <em>fmap</em> type definition above and think of <em>f</em> as a context. <em>f a</em> represents an <em>a</em> in this context. <em>f b</em> represents a <em>b</em> in the same type of context. Now imagine that context is a list, and therefore that <em>f a</em> is a list of <em>a</em>. As it turns out, <em>map</em> is the list's implementation of <em>fmap</em> that makes it a Functor instance. Nice!
<h3>A C# Implementation</h3>

But can we do this in C#? The <em>Select</em> method uses generic type variables to give its parameters the same flexibility that Haskell's <em>map</em> function has. It uses <em>IEnumerable</em> as its context in place of Haskell's list. Now we're looking to define a new generic type that could take the place of <em>IEnumerable</em>. If different functor types have different implementations of <em>fmap</em>, we're headed for some polymorphism here. If we're going to implement <em>fmap</em> polymorphically in C# we'll need an interface and at least one class. Since we're going OO here, we'll eliminate the input parameter we included before and replace it with a self reference (this) in the implementation.

First the interface:
{% highlight csharp %}
interface IFunctor<TSource> {
    IFunctor<TResult> FMap<TResult>(Func<TSource, TResult> func);
}
{% endhighlight %}
Simple enough. Now for a class to implement it. Let's try a list. It will implement IEnumerable<TSource> for its list-ness, and IFunctor<TSource> for its functor-ness.
{% highlight csharp %}
class MyEnumerable<TSource> :
    IEnumerable<TSource>, IFunctor<TSource>
{% endhighlight %}
Now for a bare-bones, boilerplate implementation of IEnumerable<TSource>, we'll just take an IEnumerable<TSource> in the constructor and delegate any IEnumerable concerns to it.
{% highlight csharp %}
    private readonly IEnumerable<TSource> _collection;

    public MyEnumerable(IEnumerable<TSource> collection) {
        _collection = collection;
    }

    public IEnumerator<TSource> GetEnumerator() {
        return _collection.GetEnumerator();
    }

    IEnumerator IEnumerable.GetEnumerator() {
        return GetEnumerator();
    }
{% endhighlight %}
Thanks to <em>System.Linq</em> and the <em>Select</em> extension, <em>MyEnumerable</em> already has the ability to map functions over itself, but it can't join in any functor games until it implements <em>IFunctor</em>'s <em>FMap</em>. Fortunately, we can have <em>FMap</em> delegate to <em>Select</em>, since it already does the same thing. <em>Select</em> takes the value from the context (<em>MyEnumerable</em>) and applies the function, but it's still up to us to put the result back into context, which we can easily do by passing it to <em>MyEnumerable</em>'s constructor.
{% highlight csharp %}
    public MyEnumerable<TResult> FMap<TResult>(
        Func<TSource, TResult> func
    ) {
        return new MyEnumerable<TResult>(this.Select(func));
    }
{% endhighlight %}
There's the <em>MyEnumerable</em>-specific implementation. Now let's just add the explicit interface implementation and have it delegate to the method we just added.
{% highlight csharp %}
    IFunctor<TResult> IFunctor<TSource>.FMap<TResult>(
        Func<TSource, TResult> func
    ) {
        return FMap<TResult>(func);
    }
{% endhighlight %}
That's it. We have implemented a functor in C#. Let's try it out before we move on. Say we have a function that takes an integer and returns a string of its square.
{% highlight csharp %}
string SquareString(int i) {
    return (i * i).ToString();
}
{% endhighlight %}
And we have a list of integers that we want to map this function over.
{% highlight csharp %}
MyEnumerable<int> list = new MyEnumerable(new[] {1, 2, 3, 4});
MyEnumerable<string> squareStrings = list.FMap(SquareString);
//=> {"1", "4", "9", "16"}
{% endhighlight %}
Of course, in C# we tend to love lambdas, and we could just as easily do it that way.
{% highlight csharp %}
squareStrings = list.FMap(i => (i * i).ToString());
//=> {"1", "4", "9", "16"}
{% endhighlight %}
But wait a minute. We haven't done anything but wrap <em>Select</em>, have we? So far, yes, but think about this: What if the context we want to map over is not a listy kind of thing? Another functor type in Haskell is <em>Maybe a</em>, which is just like .NET's <em>Nullable&lt;T&gt;</em>. It has a particular type, and it may or may not have a value. Just like a list, it provides a context for particular type of value, only in this case the context is the possibility of there being no value. Let's throw together our own homegrown nullable class.
{% highlight csharp %}
class MyNullable<TSource> : IFunctor<TSource>
{% endhighlight %}
The guts are pretty simple.
{% highlight csharp %}
    private readonly bool _hasValue;
    private readonly TSource _value;

    public MyNullable() {}

    public MyNullable(TSource value) {
        _hasValue = true;
        _value = value;
    }

    public bool HasValue {
        get { return _hasValue; }
    }

    public TSource Value {
        get { 
            if (_hasValue) return _value;
            throw new InvalidOperationException(
                "Nullable object must have a value.");
        }
    }
{% endhighlight %}
Now for the FMap implementation. Once again, we take the value from the context, apply the function, and wrap it up in context. In this case, if the MyNullable&lt;TSource&gt; instance has a value, we apply the function and put the result into a new MyNullable&lt;TResult&gt;. If there is no value, we simply return a new empty MyNullable&lt;TResult&gt;.
{% highlight csharp %}
    public MyNullable<TResult> FMap<TResult>(
        Func<TSource, TResult> func
    ) {
        return HasValue 
            ? new MyNullable<TResult>(func(Value)) 
            : new MyNullable<TResult>();
    }

    IFunctor<TResult> IFunctor<TSource>.FMap<TResult>(
        Func<TSource, TResult> func
    ) {
        return FMap(func);
    }
{% endhighlight %}
Now let's see what MyNullable can do.
{% highlight csharp %}
var nullInt = new MyNullable<int>();
// nullInt.HasValue == false

var three = new MyNullable<int>(3);
// three.HasValue == true
// three.Value == 3

var ss3 = three.FMap<string>(SquareString);
// ss3.HasValue == true
// ss3.Value == "9"

var ssNull = nullInt.FMap<string>(SquareString);
// ssNull.HasValue = false
{% endhighlight %}
<h3>Conclusion</h3>

Okay, so we have implemented Haskell's Functor in C#. What is the value in this? Remember the goal of this exercise was not to bring C# something that I thought it needed, but rather to help me understand what Functors are doing for us in Haskell. What I find here is that it's all about separation of concerns. The benefit of the separation is in the reusability of the functions that we pass to <em>fmap</em>.

Each Functor, or type context, has its own set of concerns that are not the business of any function we might want to map over it. The list and <em>MyEnumerable</em> need to iterate, call the specified function multiple times, and collect the results into a new list. The <em>Maybe</em> and <em>MyNullable</em> need to deal with the absence of a value and possibly not call the specified function at all. This Functor mechanism enables simple functions such as <em>SquareString</em> to be reused in these and limitless other contexts that they are unaware of. Each Functor context can take any simple function, and each simple function can be used in any Functor context. Without the Functor, we would have to code each function-context combination separately. With it, we have a pallette of primary colors that can be combined in unforeseen and beautiful ways.
