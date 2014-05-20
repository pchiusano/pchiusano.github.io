---
layout: post
title: Is network I/O always an effect worth tracking?
categories: [fp]
---

If you look at the applications people write these days, you'll notice that a large (often a majority) of the code in an application is devoted to explicit parsing, serialization, and networking.

In FP, we get to choose what effects are worth tracking in types. Though there are some standard choices, it is a _choice_. We don't typically track memory allocation as a typed effect, but we do track file and network I/O. The advantage to _not_ tracking an effect is that, without any sort of explicit effect polymorphism (for instance being parametric in the choice of monad), functions are more general, in particular higher order functions, which work for all possible combinations of the presence or abscence of an effect.

This isn't cheating, it's an aesthetic choice that one makes as the programmer. Haskell does not track evaluation as an effect--both thunks and forced values have the same type in Haskell (ignoring `Int#` and the like). The advantage to this is we avoid a combinatorial explosion of different functions (or lots of explicit effect polymorphism) for dealing with strictness and evaluation. My favorite example of this is the `map` function:

~~~ Haskell
map :: (a -> b) -> [a] -> [b]
~~~

In Haskell, we may pass an `a -> b` which is lazy, and avoids evaluating the `a` it is given, or a strict `a -> b`. Both variants are useful, and in a strict language, we should in theory define two versions of `map`, one which takes a strict function, and another which takes a non-strict function. (In practice, no one bothers with this sort thing in the standard libraries, so strict languages are often unnecessarily so, at a loss to modularity)

Chapter 13 of [FP in Scala](manning.com/bjarnason) discusses these issues in more detail, and [Runar also has a nice post](http://blog.higher-order.com/blog/2012/09/13/what-purity-is-and-isnt/) that formed the starting point for that chapter.

### Should we always track network I/O? ###

