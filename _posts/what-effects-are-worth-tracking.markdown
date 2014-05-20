---
layout: post
title: Is network I/O always an effect worth tracking?
categories: [fp]
---

In FP, we get to choose what effects are worth tracking in types. Though there are some standard choices, it is a _choice_. We don't typically track memory allocation as a typed effect, nor partiality, but we do track file and network I/O. The advantage to _not_ tracking an effect is that, without any sort of explicit effect polymorphism (for instance being parametric in the choice of monad), functions are more general, in particular higher order functions, which work for all possible combinations of the presence or abscence of an effect.

This isn't cheating, it's an aesthetic choice that one makes as the programmer. Haskell does not track evaluation as an effect--both thunks and forced values have the same type in Haskell (ignoring `Int#` and the like). The advantage to this is we avoid a combinatorial explosion of different functions (or lots of explicit effect polymorphism) for dealing with strictness and evaluation. My favorite example of this is the `map` function:

~~~ Haskell
map :: (a -> b) -> [a] -> [b]
~~~

In Haskell, we may pass an `a -> b` which is lazy, and avoids evaluating the `a` it is given, or a strict `a -> b`. Both variants are useful, and in a strict language, we should in theory define two versions of `map`, one which takes a strict function, and another which takes a non-strict function. (In practice, no one bothers with this sort thing in the standard libraries, so strict languages are often unnecessarily so, at a loss to modularity)

Chapter 13 of [FP in Scala](manning.com/bjarnason) discusses these issues in more detail, and [Runar also has a nice post](http://blog.higher-order.com/blog/2012/09/13/what-purity-is-and-isnt/) that formed the starting point for that chapter.

### Should we always track file and network I/O as an effect? ###

Suppose I am writing a piece of code that fetches data from the file `/foo/bar/baz.bin`, and I am prepared to assume that the contents of that file will never change over the course of my running application. Perhaps I can't truly guarantee that this assumption will hold, but so what? A gamma ray could flip some bits in the region of memory corresponding to my purely functional data structure, and I can't guarantee this won't occur. Even if you consider the possibility of gamma ray corruption to be a bit lower than the possibility of some external process mucking with the file system, there is no _fundamental_ distinction. At the end of the day, we as programmers get to decide what possibilities we want to account for and model explicitly in our programs.

One might argue that reading from a file uses a file handle, which are in limited in number by the OS, and therefore this resource acquisition and freeing is worth tracking as an effect. But actually, this constraint is artificial--one can imagine an OS in which file handles were no more scarse than memory. Or we could simply imagine a more sophisticated I/O manager which makes the underlying OS file handle scarcity irrelevant to the vast majority of programs. For the sake of this thought experiment, let's imagine such a world. Do we still need to track reading the file `/foo/bar/baz.bin` as an effect?

Not necessarily. From the perspective of our program's meaning, we might not care whether the contents of the file are fetched during the execution of the program, or whether the file contents were fetched ahead of time and we are just reading from an in-memory literal. The only way we can distinguish between these two programs (the one which fetches ahead of time, and the one which fetches during the running of the program) is that the one which fetches the data during the running of the program may have greater running time and/or possibility of failure. But we already quotient running time and possibility of failure out of our program's meaning, so the two programs can be considered semantically identical!

