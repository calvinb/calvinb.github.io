---
layout: post
title: 'Polyglot Survival Guide: Erlang, Haskell, F#, and Elixir'
date: 2014-01-20 09:43:00.000000000 -06:00
categories: haskell functional f# erlang elixir
---
Lately I have been bouncing back and forth between Erlang and Haskell, two profoundly significant functional languages that I find both very different and very similar. They are very different in their philosophies and thought patterns, particularly around data types, but both being non-LISPy functional languages, they have enough similarities that I sometimes having trouble keeping them straight when I am coding. So I have started a simple list here cataloging some of their differences side by side. I imagine I will continue to add to it over time. Please comment to let me know what other features or constructs you might like to see added here.

I have now added entries for F# and Elixir, which I am also working with lately. While Elixir is the least adopted so far, I put it right after Erlang, since their relationship leads to a lot of similarities in the details cataloged here.
<h5>Functional Purity</h5>
<dl><dt>Erlang</dt><dd>no</dd><dt>Elixir</dt><dd>no</dd><dt>Haskell</dt><dd>yes (in that all side effects are relegated to the IO monad)</dd><dt>F#</dt><dd>no</dd></dl>
<h5>REPL</h5>
<dl><dt>Erlang</dt><dd>
<pre>erl</pre>
</dd><dt>Elixir</dt><dd>
<pre>iex</pre>
</dd><dt>Haskell</dt><dd>
<pre>ghci</pre>
</dd><dt>F#</dt><dd>
<pre>fsi</pre>
</dd></dl>
<h5>Exiting the REPL</h5>
<dl><dt>Erlang</dt><dd>
<pre>q().</pre>
</dd><dt>Elixir</dt><dd>
<pre>ctrl-c ctrl-c</pre>
or
<pre>ctrl-g q</pre>
</dd><dt>Haskell</dt><dd>
<pre>:q</pre>
</dd><dt>F#</dt><dd>
<pre>#quit;;</pre>
</dd></dl>
<h5>Line terminators</h5>
<dl><dt>Erlang</dt><dd>English-like (, ; .)</dd><dt>Elixir</dt><dd>none</dd><dt>Haskell</dt><dd>none</dd><dt>F#</dt><dd>none</dd></dl>
<h5>Typing</h5>
<dl><dt>Erlang</dt><dd>dynamic</dd><dt>Elixir</dt><dd>dynamic</dd><dt>Haskell</dt><dd>static with inference</dd><dt>F#</dt><dd>static with inference</dd></dl>
<h5>I/O</h5>
<dl><dt>Erlang</dt><dd>io, file, and some other modules</dd><dt>Elixir</dt><dd>IO, File, and some other modules</dd><dt>Haskell</dt><dd>IO monad</dd><dt>F#</dt><dd>.NET Framework</dd></dl>
<h5>Printing</h5>
<dl><dt>Erlang</dt><dd>
<pre>io:format("Hello~n").</pre>
</dd><dt>Elixir</dt><dd>
<pre>IO.puts "Hello\n"</pre>
</dd><dt>Haskell</dt><dd>
<pre>putStrLn "Hello"</pre>
</dd><dt>F#</dt><dd>
<pre>printfn "Hello"</pre>
</dd></dl>
<h5>Variable names</h5>
<dl><dt>Erlang</dt><dd>
<pre>PascalCase</pre>
</dd><dt>Elixir</dt><dd>
<pre>snake_case</pre>
</dd><dt>Haskell</dt><dd>
<pre>camelCase</pre>
</dd><dt>F#</dt><dd>
<pre>camelCase</pre>
</dd></dl>
<h5>Atoms</h5>
<dl><dt>Erlang</dt><dd>
<pre>atom</pre>
</dd><dt>Elixir</dt><dd>
<pre>:atom</pre>
</dd><dt>Haskell</dt><dd>(none)</dd><dt>F#</dt><dd>(none)</dd></dl>
<h5>Laziness</h5>
<dl><dt>Erlang</dt><dd>hackable with funs</dd><dt>Elixir</dt><dd>streams lazy, lists not</dd><dt>Haskell</dt><dd>by default</dd><dt>F#</dt><dd>sequences lazy, lists not; explicit with <em>lazy</em> keyword</dd></dl>
<h5>Infinite lists</h5>
<dl><dt>Erlang</dt><dd>hackable with funs</dd><dt>Elixir</dt><dd>streams, yes; lists, no</dd><dt>Haskell</dt><dd>absolutely</dd><dt>F#</dt><dd>sequences with <em>yield</em></dd></dl>
<h5>Currying</h5>
<dl><dt>Erlang</dt><dd>explicit with lambdas, no dedicated syntax</dd><dt>Elixir</dt><dd>explicit with lambdas, no dedicated syntax</dd><dt>Haskell</dt><dd>implicit</dd><dt>F#</dt><dd>implicit</dd></dl>
<h5>Side effects</h5>
<dl><dt>Erlang</dt><dd>yes</dd><dt>Elixir</dt><dd>yes</dd><dt>Haskell</dt><dd>never, except via the IO monad</dd><dt>F#</dt><dd>yes</dd></dl>
<h5>Tuples</h5>
<dl><dt>Erlang</dt><dd>
<pre>{"foo", "bar"}</pre>
</dd><dt>Elixir</dt><dd>
<pre>{"foo", "bar"}</pre>
</dd><dt>Haskell</dt><dd>
<pre>("foo", "bar")</pre>
</dd><dt>F#</dt><dd>
<pre>"foo", "bar"</pre>
</dd></dl>
<h5>List construction/matching</h5>
<dl><dt>Erlang</dt><dd>
<pre>[Head|Tail]</pre>
</dd><dt>Elixir</dt><dd>
<pre>[h|t]</pre>
</dd><dt>Haskell</dt><dd>
<pre>x:xs</pre>
</dd><dt>F#</dt><dd>
<pre>x :: xs</pre>
</dd></dl>
<h5>Heterogeneous lists</h5>
<dl><dt>Erlang</dt><dd>yes</dd><dt>Elixir</dt><dd>yes</dd><dt>Haskell</dt><dd>no</dd><dt>F#</dt><dd>no</dd></dl>
<h5>Function calls</h5>
<dl><dt>Erlang</dt><dd>
<pre>add(1,2)</pre>
</dd><dt>Elixir</dt><dd>
<pre>add(1,2)</pre>
or
<pre>add 1,2</pre>
</dd><dt>Haskell</dt><dd>
<pre>add 1 2</pre>
</dd><dt>F#</dt><dd>
<pre>add 1 2</pre>
</dd></dl>
<h5>Lambdas</h5>
<dl><dt>Erlang</dt><dd>
<pre>fun(X,Y) -&gt; X + Y end</pre>
</dd><dt>Elixir</dt><dd>
<pre>fn x,y -&gt; x + y end</pre>
or
<pre>&amp;(&amp;1 + &amp;2)</pre>
</dd><dt>Haskell</dt><dd>
<pre>\x y -&gt; x + y</pre>
</dd><dt>F#</dt><dd>
<pre>fun x y -&gt; x + y</pre>
</dd></dl>
<h5>List comprehensions</h5>
<dl><dt>Erlang</dt><dd>
<pre>[X || X &lt;- [1,2,3,4,5], X &gt; 3]</pre>
</dd><dt>Elixir</dt><dd>
<pre>for x &lt;- [1,2,3,4,5], x &gt; 3, do: x</pre>
</dd><dt>Haskell</dt><dd>
<pre>[x | x &lt;- [1,2,3,4,5], x &gt; 3]</pre>
</dd><dt>F#</dt><dd>
<pre>{ for x in 1 .. 5 when x &gt; 3 -&gt; x }</pre>
</dd></dl>
<h5>Converting integer to string</h5>
<dl><dt>Erlang</dt><dd>
<pre>integer_to_list(123)</pre>
</dd><dt>Elixir</dt><dd>
<pre>to_string 123</pre>
</dd><dt>Haskell</dt><dd>
<pre>show 123</pre>
</dd><dt>F#</dt><dd>
<pre>string 123</pre>
</dd></dl>
<h5>Comments</h5>
<dl><dt>Erlang</dt><dd>
<pre>% comment after percent</pre>
</dd><dt>Elixir</dt><dd>
<pre># comment after pound sign</pre>
</dd><dt>Haskell</dt><dd>
<pre>-- comment after double hyphen</pre>
</dd><dt>F#</dt><dd>
<pre>// comment after double slash</pre>
</dd></dl>
<h5>Mapping</h5>
<dl><dt>Erlang</dt><dd>
<pre>lists:map(fun(X) -&gt; X*X end, [1,2,3]).</pre>
</dd><dt>Elixir</dt><dd>
<pre>Enum.map [1,2,3], &amp;(&amp;1 * &amp;1)</pre>
</dd><dt>Haskell</dt><dd>
<pre>map (x -&gt; x*x) [1..3]</pre>
</dd><dt>F#</dt><dd>
<pre>List.map (fun x -&gt; x*x) [1 .. 3]</pre>
</dd></dl>&nbsp;
