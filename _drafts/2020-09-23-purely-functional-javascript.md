---
layout: post
title: Purely Functional JavaScript?
categories: functional javascript haskell
---
Several years ago when I first began learning about functional programming,
Haskell caught my attention. I read that among the functional languages I was
comparing (mainly Erlang and LISP), Haskell was the only one that was "purely" functional.
The others had made certain compromises for the sake of being useful; Haskell didn't care.
This intrigued me.

What does it mean to be *purely* functional?

1. Functions' results are determined by their inputs and nothing else.
1. Functions have no side effects.

It makes sense that most functional languages compromise on these rules; if you follow
them to the letter, you can't create anything useful at all. Haskell preserves its claim
to purity by encapsulating all impure operations in its IO action. The cool thing is the
way Haskell formally separates the impure from the pure. In our programs there is plenty
of logic that can be defined in a pure way, but there are also real-world things to
read from and write to that break the rules.

